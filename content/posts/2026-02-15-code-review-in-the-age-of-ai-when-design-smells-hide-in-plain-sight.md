---
title: "Code Review in the Age of AI: When Design Smells Hide in Plain Sight"
date: 2026-02-15
type: post
series: ["FastMCP Debugging"]
series_order: 3
---

# Code Review in the Age of AI: When Design Smells Hide in Plain Sight

I used to review code for correctness. Does it work? Is the logic sound? Are the tests passing? Ship it.

Then I started using AI to fix my bugs, and I discovered something uncomfortable: AI will maintain backwards compatibility with code that never worked in the first place.

But this time, I caught it before shipping.

## The Original Disaster

Here's how it started. I wrote this function:

```
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,
) -> AnalysisResult:
    """Analyze PR comments"""
    return analyze(pr_context)

```

Clean. Simple. One parameter. I wrote unit tests that passed a dataclass instance directly, they passed, I shipped it.

Then production crashed: `AttributeError: 'dict' object has no attribute 'organization'`

The dataclass parameter couldn't survive MCP serialization ([full story here](../Post 1 of 4 - Why Your Tests Passed But Your API Failed - The SerDe Boundary Problem.md)). So I asked AI to fix it.

## The "Fix" That Almost Shipped

AI's solution?

```
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,  # STILL HERE
    pr_url_or_id: Optional[str] = None,                 # NEW WORKING PATH
    organization: Optional[str] = None,
    project: Optional[str] = None,
    repository: Optional[str] = None,
) -> AnalysisResult:
    """Analyze PR comments - accepts context object OR individual parameters"""
    if pr_context:
        return analyze(pr_context)  # Still broken!
    else:
        # This path works fine
        context = establish_context(pr_url_or_id, org, project, repo)
        return analyze(context)

```

![Dog surrounded by flames: "This is fine."](https://imgflip.com/s/meme/This-Is-Fine.jpg)_

AI added a working path but *kept the broken parameter* "for backwards compatibility."

Backwards compatibility with what, exactly? Code that never worked? There are no other consumers—the only caller is the AI itself!

## The Moment I Caught It

This time, I actually looked at the code. Not just "does the new path work"—I looked at the *whole signature*.

Five optional parameters. Two completely different call patterns. One of them still broken.

Wait a minute.

I just got burned shipping code I didn't review carefully enough. I'm not doing that again.

So I did what I should have done in the first place: a full audit of all my MCP tools. How many other functions had dataclass parameters lurking in their signatures?

Turns out: several. Some marked with TODO comments, some just sitting there waiting to crash.

## The Pattern: AI as the Overly-Cautious Maintainer

Once I saw the pattern, it was obvious. AI has a strong bias toward backwards compatibility, even when it makes no sense.

Think about AI's training data:

- Millions of pull requests where reviewers say "this breaks existing callers"
- Stack Overflow answers explaining how to maintain backwards compatibility
- Deprecation patterns showing how to support old signatures
- Code reviews praising "backwards compatible changes"

AI learned that breaking changes are bad. It learned to keep old interfaces even when adding new ones. It learned to maintain multiple code paths.

What it didn't learn: Sometimes the old code path *should die*.

Especially when:

1. The old path never worked in production
2. There are no external consumers
3. The only caller is the AI itself
4. You're fixing a fundamental design problem, not adding a feature

## You Have Yet to Convince Me, Sir!

"But Jack," you're thinking, "you caught it! What's the problem?"

The problem is I *almost* didn't. Here's what nearly happened:

1. See the production crash
2. Ask AI to fix it
3. See that the new code path works
4. Ship it without looking at the full signature
5. Maintain five optional parameters forever
6. Keep zombie code paths that never worked

I caught it this time because I'd just been burned. But how many times have I *not* caught it? How many dual-signature footguns are sitting in codebases right now because developers reviewed for "does the fix work" instead of "should this design exist?"

The cognitive load shifted entirely. I'm no longer checking if the code *works*—AI handled that. I'm checking if the code *should exist in this form at all.*

## The Real Red Flags

Here's what I learned to watch for when AI "fixes" my code:

### 🚩 Red Flag #1: Optional Parameter Proliferation

```
# AI will happily do this
def do_thing(
    old_param: Optional[OldType] = None,     # The "backwards compatible" one
    new_param: Optional[str] = None,         # The actual fix
    extra_param1: Optional[int] = None,
    extra_param2: Optional[dict] = None,
) -> Result:
    ...

```

Question to ask: "How many ways can this function be called? Should it be that many?"

### 🚩 Red Flag #2: The Zombie Code Path

```
# AI keeps the broken path alive
def process(
    broken_param: Optional[BrokenType] = None,  # Never worked!
    working_param: Optional[str] = None,        # The fix
) -> Result:
    if broken_param:
        return old_way(broken_param)  # This path is dead, Jim
    else:
        return new_way(working_param)

```

Question to ask: "Is the old path actually used by anyone, anywhere?"

### 🚩 Red Flag #3: Backwards Compatibility With Nothing

```
# No external callers, but AI maintains "compatibility"
def my_internal_function(
    old_style: Optional[OldType] = None,
    new_style: Optional[NewType] = None,
) -> Result:
    # No one else calls this function!
    # Why am I supporting two interfaces?
    ...

```

Question to ask: "Who am I maintaining compatibility for?" (If the answer is "no one," delete the old path)

![Expanding brain - Level 1: "AI generates code", Level 2: "AI fixes bugs", Level 3: "AI maintains backwards compatibility", Level 4: AI maintains backwards compatibility with broken code that has no callers"](https://i.imgflip.com/ak8ek1.jpg)

## What I Actually Did: The Code Audit

After catching the dual-signature smell, I audited all my MCP tools:

```
# Search for dataclass parameters in @mcp.tool() functions
grep -A 10 "@mcp.tool()" src/**/*.py | grep "dataclass"

```

Found them in:

- `post_ai_generated_comments` - List[CodeReviewComment] parameter
- `post_ai_comments_by_pr_url` - List[CodeReviewComment] parameter
- Several others already marked with TODO comments

I had AI fix them all, but this time with explicit instructions:

❌ Before: "Fix this function to work with MCP serialization"

✅ After: "Remove the dataclass parameter entirely and replace it with primitives. No backwards compatibility needed—there are no external callers."

Be explicit about:

- Remove the broken code (don't keep it "for compatibility")
- No backwards compatibility needed (this is internal, no external callers)
- Single code path (I don't want multiple ways to call this)

AI's default is additive. You have to explicitly tell it to *remove* things.

## The Fix I Actually Shipped

Here's what I rejected:

```
# AI's "backwards compatible" fix - REJECTED
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,  # Zombie parameter
    pr_url_or_id: Optional[str] = None,
    organization: Optional[str] = None,
    project: Optional[str] = None,
    repository: Optional[str] = None,
) -> AnalysisResult:
    ...

```

Here's what I actually shipped:

```
# The actual fix - ACCEPTED
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_url_or_id: str,  # One parameter. Clean. Works.
) -> AnalysisResult:
    """Analyze PR comments"""
    context = establish_context(pr_url_or_id)
    return analyze(context)

```

Five optional parameters became one required parameter. Two code paths became one. The zombie parameter is gone. The API is clean.

All my other tools got the same treatment. Dataclass parameters? Gone. Complex objects at the API boundary? Removed. Primitives in, complex objects out.

![Drake disapproving: "AI fix that maintains backwards compatibility with broken code", Drake approving: "AI fix that removes the broken code entirely"](https://i.imgflip.com/ak8ese.jpg)

## The Systematic Review Checklist

After this experience, here's my checklist for reviewing AI-generated fixes:

1. Look at the full signature, not just the fix

  - How many parameters?
  - How many are optional?
  - Would I write this by hand?

2. Check for zombie code paths

  - Are there multiple ways to call this?
  - Is the old way actually used?
  - Can I delete it?

3. Ask who the compatibility is for

  - Are there external callers?
  - Is this a public API?
  - Or is it just me and the AI?

4. Do a broader audit when you find a pattern

  - If one function has the problem, others probably do too
  - Search the codebase
  - Fix them all at once

5. Be explicit in your instructions

  - "Remove X" not "Fix X"
  - "No backwards compatibility needed"
  - "Single code path only"

The key insight: AI doesn't know what should die. It only knows how to add, maintain, and preserve. You're the human who decides what gets deleted.

## My Admittedly Biased Conclusion

AI-assisted development changes what we review for:

1. AI's prime directive is "don't break things" - even when breaking is the right move
2. Backwards compatibility with nothing is still compatibility - AI will maintain it
3. Be explicit about removal - "Remove X and replace with Y", not just "Fix X"
4. Do broad audits - If you find one problem, search for others
5. One code path is better than two - Especially when one never worked

I caught this one. I did the audit. I fixed them all properly. But I almost didn't—and that's the trap.

The future isn't "AI writes perfect code." The future is "AI writes overly-cautious code, and humans make the hard calls about what should die."

Be the human who asks:

- Who is this backwards compatibility for?
- Is the old path actually used?
- Can we just delete this?
- Would I write this signature by hand?
- Are there other places with the same problem?

Because AI learned from thousands of "don't break existing callers" code reviews. It didn't learn to recognize when there *are no existing callers*.

That's your job now. And this time, I actually did mine.

---

## Related Posts

- [Why Your Tests Passed But Your API Failed](http://www.jackpines.info/2026/02/why-your-tests-passed-but-your-api.html) - Why my tests didn't catch it
- [The FastMCP Dataclass Parameter Trap](http://www.jackpines.info/2026/02/the-fastmcp-dataclass-parameter-trap.html) - What actually broke
- [Debugging False Memories](http://www.jackpines.info/2026/02/debugging-false-memories-case-study-in.html) - A case study in systematic investigations
-
