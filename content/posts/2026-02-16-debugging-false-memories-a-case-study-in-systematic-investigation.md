---
title: "Debugging False Memories: A Case Study in Systematic Investigation"
date: 2026-02-16
type: post
series: ["FastMCP Debugging"]
series_order: 4
---

# Debugging False Memories: A Case Study in Systematic Investigation

**"I'm pretty sure this worked before."**

Famous last words from a software engineer. I said them with confidence during my MCP dataclass debugging session. I had this vivid memory of calling the function with a dataclass parameter and watching it work in production.

Then I did the git archaeology. It never worked. Not once. The memory was completely false.

![Astronaut looking at Earth: "Wait, it's all false memories?" Astronaut aiming gun at other astronaut: "Always has been"](https://i.imgflip.com/ak8fs2.jpg)

Here's the uncomfortable part: I'm usually right about these things. Pattern recognition is one of my strengths—comes with the neurodivergent territory (AuDHD, recently diagnosed, finally explains some things). I can spot patterns across codebases, remember edge cases from months ago, and track mental state machines through complex systems.

But this time? My brain confidently remembered something that never happened.

Let me show you how that false memory formed, and more importantly, how systematic investigation caught it.

## The False Memory

Here's what I "remembered":

> 

"I deployed this function with the `pr_context` dataclass parameter. I tested it via Copilot. It worked. Then something changed—maybe a FastMCP update?—and now it's broken. I need to figure out what changed."

Confident. Specific. Completely wrong.

Here's what actually happened (according to git history, which doesn't lie):

1. Wrote function with `pr_context: AzureDevOpsPRContext` parameter
2. Wrote unit tests that passed dataclass instances directly
3. Deployed to production
4. Never actually called it with the dataclass parameter via Copilot
5. Discovered it was broken when I finally tried

The dataclass parameter never worked. Not on any version. Not on any day. I never successfully used it in production.

My memory said otherwise.

## How False Memories Form: Sampling Bias

Here's what I think happened. The function had this signature:

```
@mcp.tool()
async def azure_devops_pr_comment_analysis(
    pr_context: Optional[AzureDevOpsPRContext] = None,
    pr_url_or_id: Optional[str] = None,
    organization: Optional[str] = None,
    ...
):
    if pr_context:
        return analyze(pr_context)
    else:
        context = establish_context(pr_url_or_id, org, ...)
        return analyze(context)

```

Notice: Two optional parameters at the top. Two completely different ways to call it.

When I used the function in production (via Copilot), I always used `pr_url_or_id`. That path worked perfectly. I used it dozens of times. Every single invocation succeeded.

My brain formed a pattern: "This function works in production."

But I wasn't sampling randomly—I was only using the working code path. The broken `pr_context` path sat there untouched, never exercised via the actual MCP protocol.

Sampling bias in action: I remembered the function working because the path I actually used *did* work. My brain generalized "this function works" from "this specific code path I always use works."

## Pattern Recognition: Gift and Curse

Here's the neurodivergent angle. One of my traits is enhanced pattern recognition. I can:

- Track complex state across multiple systems
- Remember API behaviors from projects months ago
- Spot similar bugs across different codebases
- Build mental models of how systems interact

This is incredibly useful. It's why I'm good at distributed systems and debugging complex interactions.

But pattern recognition has a shadow side: pattern matching on incomplete data.

My brain saw:

- ✅ Function deployed to production
- ✅ Called function via Copilot successfully
- ✅ Function returned correct results
- ✅ No errors in logs

And concluded: "Function works in production with all its parameters."

My brain filled in the gaps. It took "works with the parameters I used" and generalized to "works with all parameters." The pattern-matching engine ran on incomplete data and generated a false memory.

The kicker: High-confidence false memories feel exactly like real memories. There's no internal flag that says "hey, you're extrapolating here." It feels like recalling something that actually happened.

## The Systematic Investigation That Caught It

Here's what saved me: I don't trust my memory for important things.

When I hit the bug in production, I didn't just accept "it worked before, something must have changed." I did git archaeology:

```
# Check the history of this function
git log -p --all -S "pr_context: Optional[AzureDevOpsPRContext]"

# Check FastMCP version changes
git log -p package.json | grep fastmcp

# Check when the function was added
git log --all --oneline -- path/to/file.py

```

The git history was unambiguous:

- Function added with dataclass parameter: January 15
- First use via Copilot using `pr_url_or_id`: January 16
- Every subsequent use: `pr_url_or_id` parameter
- Bug discovered: January 22
- No FastMCP version changes in that time

The dataclass parameter was there from day one. I never successfully used it. My memory was false.

![Debugging my own brain](https://i.imgflip.com/ak8g0o.jpg)

## The RCA Document: External Memory

Once I confirmed my memory was wrong, I documented everything in an RCA (Root Cause Analysis) document:

```
# RCA: MCP Dataclass Parameter Bug

## Timeline
- Jan 15: Function deployed with pr_context parameter
- Jan 16-21: Used function successfully via pr_url_or_id parameter
- Jan 22: Attempted to use pr_context parameter, discovered it crashes
- Jan 22: False memory identified via git archaeology

## False Memory Formation
1. Sampling bias: Only tested working code path
2. Pattern generalization: "Function works" from "this path works"
3. Dual signature footgun: Two parameters, one broken

```

The RCA document became my external memory. When my brain says "I'm pretty sure X happened," I can check the document instead of trusting the pattern-matching engine.

This is critical for neurodivergent engineers (and honestly, all engineers): Your memory is a pattern-matching system, not a recording device. It will confidently generate false memories based on incomplete data.

Write it down. All of it. Timestamps, versions, exact commands, results. The document doesn't lie.

## The Meta-Cognitive Debugging Process

Here's the systematic approach that works for me:

### 1. Notice the Confidence

When I catch myself saying "I'm pretty sure this worked before," that's a red flag now. High confidence doesn't mean accuracy—it just means my pattern-matching engine is happy with its conclusion.

Action: Write it down. "I believe X happened. Let me verify."

### 2. Seek Falsification

Don't look for evidence that confirms your memory. Look for evidence that *contradicts* it.

Action: Git log, not grep for what you expect to find. Check version history. Look at actual timestamps.

### 3. Document the Investigation

As you investigate, write down what you find in real-time. Not what you remember finding—what you're actually seeing right now.

Action: RCA document with timestamps and exact commands.

### 4. Accept When You're Wrong

If the evidence contradicts your memory, your memory is wrong. Full stop. Doesn't matter how confident you felt.

Action: Update your mental model. Document the false memory so you learn from it.

### 5. Learn the Pattern

False memories aren't random. They form from specific cognitive biases—sampling bias, confirmation bias, pattern overgeneralization.

Action: Identify *why* the false memory formed. Document the bias so you can catch it next time.

## You Have Yet to Convince Me, Sir!

"But Jack," you're thinking, "isn't this just normal debugging? Why the neurodivergent angle?"

Fair question. Here's why it matters:

For neurotypical engineers: Memory failures are bugs that happen occasionally. Usually not a big deal.

For neurodivergent engineers with enhanced pattern recognition: Memory failures are *systematic bugs in the pattern-matching engine.* They happen more often because the engine runs hotter, processes more patterns, makes more connections.

My pattern recognition is a superpower for debugging complex systems. But it generates false memories more readily than a typical memory system because it's constantly running extrapolations and filling in gaps.

The solution isn't to trust my memory less (I already don't trust it much). The solution is to systematize the external memory system so it's as strong as the pattern-matching engine.

RCA documents. Git archaeology. Timestamp everything. Write it down.

Make the documentation system as powerful as the pattern recognition system.

![Most interesting man: "I don't always trust AI-generated code, but when I do, I verify it with git log."](https://i.imgflip.com/ak8gri.jpg)

## The Practical Takeaways (For Everyone)

Whether you're neurodivergent or not, here's what I learned:

### 1. "I'm Pretty Sure" is a Red Flag

High confidence doesn't mean accuracy. When you catch yourself being very confident about a memory, that's when you should verify most carefully.

### 2. Git History Doesn't Lie

Your memory might be wrong. Git log is never wrong. When they disagree, believe git.

### 3. Write RCA Documents

Not just for outages. For bugs. For false memories. For anything where you want to understand what actually happened vs. what you thought happened.

### 4. Sampling Bias is Everywhere

If you only test the paths you use frequently, you'll think everything works. Test the paths you *don't* use too.

### 5. Pattern Recognition ≠ Memory

Just because you can spot patterns doesn't mean you accurately remember specific events. Those are different cognitive systems.

### 6. External Memory Systems

Documentation, git history, timestamps, logs. Build systems that capture what actually happened, not what your brain says happened.

## My Admittedly Biased Conclusion

I have a false memory about code that never worked. The memory was vivid, specific, and completely wrong.

This happens because my pattern-matching engine runs hot—it's one of my strengths for debugging distributed systems, and it's also why I generate false memories more readily than some engineers.

The antidote is systematic investigation:

- Don't trust high-confidence memories
- Do git archaeology
- Write RCA documents
- Build external memory systems
- Verify, don't assume

My neurodivergent traits are both the cause of the false memory (aggressive pattern matching) and the solution (systematic documentation).

The pattern-matching engine is powerful. But it needs guardrails. Git log is the guardrail.

Now when I say "I'm pretty sure this worked before," I immediately follow with "let me check the git history."

Because I've learned the hard way: my memory is a feature, not a fact.

---

## The Full Story

- [Why Your Tests Passed But Your API Failed](http://www.jackpines.info/2026/02/why-your-tests-passed-but-your-api.html) - Why my tests didn't catch it
- [The FastMCP Dataclass Parameter Trap](http://www.jackpines.info/2026/02/the-fastmcp-dataclass-parameter-trap.html) - What actually broke
- [Code Review in the Age of AI](http://www.jackpines.info/2026/02/code-review-in-age-of-ai-when-design.html) - How AI almost made it worse
- 
-
