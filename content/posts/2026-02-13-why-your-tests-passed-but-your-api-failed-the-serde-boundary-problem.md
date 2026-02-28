---
title: "Why Your Tests Passed But Your API Failed - The SerDe Boundary Problem"
date: 2026-02-13
type: post
---

# Why Your Tests Passed But Your API Failed - The SerDe Boundary Problem

I recently spent several hours debugging a bug that *shouldn't have existed*. My test suite? 510 passing tests with solid coverage. My BDD tests? Validating every tool's contract like a bouncer at an exclusive club. Yet when I deployed to production, half my API was broken.

![Tests pass - prod is on fire: This is fine](https://i.imgflip.com/ak0eao.jpg)

The tests lied. Well, not exactly lied—they just weren't testing what I *thought* they were testing.

## The Bug That Passed All My Tests

I had MCP tools that accepted dataclass parameters:

```
@dataclass
class AzureDevOpsPRContext:
    organization: str
    project: str
    repository: str
    pr_id: int

@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,
    ...
):
    org = pr_context.organization  # Works in tests, crashes in production

```

In my unit tests: ✅ All green, baby!  
Via Copilot in production: ❌ `AttributeError: 'dict' object has no attribute 'organization'`

*Chef's kiss.*

## What Was I Actually Testing?

Here's what my "comprehensive" unit test looked like:

```
# My unit test - so pure, so naive
async def test_pr_comment_analysis():
    context = AzureDevOpsPRContext(
        organization="msazure",
        project="Commerce",
        repository="PaymentsDataPlatform",
        pr_id=14679218
    )
    
    result = await azure_devops_pr_comment_analysis(pr_context=context)
    
    assert result  # Passes! Ship it!

```

This test validates:

- ✅ Python function signature is correct
- ✅ Internal logic works
- ✅ Return type is valid
- ✅ I'm a competent developer

This test does NOT validate:

- ❌ JSON deserialization
- ❌ The MCP protocol boundary
- ❌ How clients actually invoke the tool
- ❌ Literally the deployment path

![unit tests: perfect. production: on fire. integration tests: conspicuously absent.](https://i.imgflip.com/ak0czc.jpg)

## The Brutal Reality of Production

Here's what *actually* happens when a client calls my API:

Copilot (JSON-RPC client)  
↓  
Sends: {"pr_context": {"organization": "msazure", ...}}  
↓  
MCP Protocol Layer (the betrayer)  
↓  
Deserializes JSON → Python dict  
↓  
My Beautiful Function  
↓  
Receives: pr_context = {"organization": "msazure", ...} # Not a dataclass!  
↓  
Confidently executes: pr_context.organization  
↓  
💥 AttributeError: 'dict' object has no attribute 'organization'  
↓  
🔥 Production burns

My unit test completely bypassed this path. It handed my function a fully-formed Python dataclass instance, never once exercising the JSON serialization/deserialization that happens in the real world.

It's like testing a parachute by throwing it on the ground and confirming it's made of fabric.

## Wait, What's Actually Being Tested Here?

Let me spell it out with a handy table, because I'm nothing if not a documentation enthusiast:Test TypeWhat It TestsExercises SerDe?Catches This Bug?Unit testPython APINoNoIntegration testFull protocolYesYes

My 100% test coverage was testing the *wrong layer entirely*. I had perfect coverage of my Python semantics while my protocol semantics burned in production.

## You Have Yet to Convince Me, Sir!

"But Jack," I hear you saying, "surely you just wrote bad tests?"

![REST, gRPC, GraphQL, and MCP all pointing at each other: same SerDe footgun, different protocol](https://i.imgflip.com/ak0ejy.jpg)

Fair. But here's why this matters: every API that crosses a serialization boundary has this exact footgun waiting for you.

REST? Check.  
gRPC? Check.  
GraphQL? Check.  
MCP? Check.  
That custom binary protocol you swore you'd document? Definitely check.

If your tests don't exercise the actual wire protocol, they're testing a fantasy version of your system.

## So, Now What?

The fix? Write tests that actually exercise the protocol:

```
async def test_pr_comment_analysis_via_mcp():
    """Test tool invocation through actual MCP protocol"""
    
    # This goes through the full serialization cycle
    result = await mcp_client.call_tool(
        "azure_devops_pr_comment_analysis",
        {
            "pr_context": {
                "organization": "msazure",
                "project": "Commerce",
                "repository": "PaymentsDataPlatform",
                "pr_id": 14679218
            }
        }
    )
    
    # Now we're testing what clients actually do

```

This test caught the bug *immediately*. The dataclass parameter didn't deserialize from JSON to a Python dataclass—it stayed as a dict, exactly as the protocol specified.

## The Actual Fix (AKA: How I Should Have Done It)

python

```
# Before (broken in production, passed all tests)
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,
    ...
):
    org = pr_context.organization

# After (works everywhere, imagine that)
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_url_or_id: str,
    ...
):
    # Construct dataclass internally from primitive
    context = azure_devops_establish_pr_context(pr_url_or_id)
    org = context.organization

```

The pattern: Primitives in, complex objects out. Construct your dataclasses *internally* where you control the object creation, not at the API boundary where JSON deserialization is a wild west of "good luck, cowboy."

## Why AI-Assisted Development Makes This Worse

When you hand-write code, you think about the deployment path. You remember that time in 2019 when serialization bit you, and you code defensively.

When AI generates code, it optimizes for *passing your tests*. It looks at your test suite, sees the pattern, and generates more code that follows that pattern. If your tests don't exercise the actual protocol boundary, AI will happily generate code that works beautifully in tests and catastrophically in production.

![They're (not) the same picture meme: code that passes tests and code that works in production](https://i.imgflip.com/ak0d7y.jpg)

My tests were well-written. My coverage was high. My BDD contracts were solid. My CI was green. And I had a completely broken API in production because *none of it tested the actual deployment path*.

## My Admittedly Biased Conclusion

Here's what I learned the hard way:

1. Unit tests validate Python semantics, not protocol semantics
2. Integration tests must exercise the actual client→server path
3. 100% coverage means nothing if you're testing the wrong layer
4. SerDe boundaries are where type assumptions go to die
5. AI-generated code amplifies this problem - it passes your tests without understanding your deployment

If you're building APIs that cross serialization boundaries—and unless you're writing a desktop calculator app, you probably are—write integration tests that actually invoke through the protocol. Your unit tests are lying to you, and they don't even feel bad about it.

Don't ask me how I know.

---

## Related Posts

- [The FastMCP Dataclass Parameter Trap](https://www.jackpines.info/2026/02/the-fastmcp-dataclass-parameter-trap.html) - Technical deep-dive on the specific MCP framework issue
- [Code Review in the Age of AI](http://www.jackpines.info/2026/02/code-review-in-age-of-ai-when-design.html) - When design smells hide in plain sight
- [Debugging False Memories](http://www.jackpines.info/2026/02/debugging-false-memories-case-study-in.html) - A case study in systematic investigations
-
