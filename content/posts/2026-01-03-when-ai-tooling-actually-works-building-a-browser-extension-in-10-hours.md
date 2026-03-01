---
title: "When AI Tooling Actually Works: Building a Browser Extension in 10 Hours"
date: 2026-01-03
slug: "when-ai-tooling-actually-works-building-a-browser-extension-in-10-hours"
tags: ["AI", "browser extension", "productivity"]
description: "Yes, I'm writing a *positive* post about AI-assisted development. I know, I'm as shocked as you are. If you've read my previous posts, you know I'm not..."
aliases:
  - "/2026/01/when-ai-tooling-actually-works-building.html"
---

![](/images/posts/blogger-f799f6d1d0.png)[](/images/posts/blogger-f799f6d1d0.png)  

Yes, I'm writing a *positive* post about AI-assisted development. I know, I'm as shocked as you are. If you've read my previous posts, you know I'm not exactly bullish on enterprise AI tooling that promises to replace developers. But here's the thing: when you treat AI as a productivity tool rather than a replacement for engineering discipline, some interesting things can happen.

Case in point: I built a fully-tested, TypeScript-based browser extension from concept to Microsoft Edge Extension store submission in roughly 10 hours. And yes, Claude 4.5 (via GitHub Copilot) was instrumental in making that happen.

## The Context: A Hiring Manager's Gift

Recently, I had a conversation with a hiring manager who dismissed me because I didn't have TypeScript experience. Never mind the 15+ years of software engineering across multiple languages and platforms - no TypeScript checkbox meant no interview.

******![](/images/posts/blogger-638353d683.png)[](/images/posts/blogger-638353d683.png)****  
**

So, I decided to fix that gap while solving an actual problem I'd been meaning to address: YouTube's web interface doesn't give you the "Videos" tab when viewing a channel like it does on mobile. Instead, it drops you into the "Home" tab where featured videos are displayed. That may sound just fine to you, but to me, an avid subscriber to many channels, it's rather annoying to have to click to "Videos" to see their latest work everytime. So, I finally scratched that itch.

## The Workflow That Actually Worked

Here's what I did differently than the typical "AI will do everything" approach:

### 1. Started with BDD Test Specifications

I didn't just say "build me an extension." I started with stating my goal and asking for a design doc for me to review. The biggest mistake many make is allowing it to code off a single prompt, and then chasing the AI into seeing your vision. (The same mistake happens in many enterprise organizations with humans at the wheel, too.) I then shared my Behavior-Driven Development testing documentation framework ([see my prior post on BDD](/posts/the-great-testing-reframe-from-implementation-driven-to-value-driven-development/)). This gave the AI:

- Clear acceptance criteria
- Expected behaviors
- Edge cases to consider
- A quality bar to meet

******![](/images/posts/blogger-18a81fa795.png)[](/images/posts/blogger-18a81fa795.png)****  
**

### 2. Incremental Iteration

Initial implementation was JavaScript (perfectly appropriate for a browser extension). But then I asked: "Would TypeScript make sense here?"

The AI pivoted seamlessly, rebuilt the solution in TypeScript, and I got hands-on experience with the language that hiring manager wanted. Turns out, learning a new language when you have 15+ years of experience in others isn't actually that hard.

### 3. Verification of Claims

Here's where it gets interesting. The AI confidently reported 100% code coverage.

I checked. It was 57%.

******![](/images/posts/blogger-7f3d780671.png)[](/images/posts/blogger-7f3d780671.png)****  
**

When I called it out, it didn't argue. It worked to actually achieve 100% coverage. It got to 97% (remember my past discussion of AI laziness?), but asked me if I wanted to push for 100%. I reminded it that BDD is all about specifying what a system will do, and 97% is 3% unspecified. This is the critical difference: *I verified the claims*. An inexperienced developer might have just believed the initial report and moved on with inadequate test coverage.

### 4. Real Problem-Solving

Theory met reality when the extension didn't work in the browser. Turns out, there's non-trivial complexity in making TypeScript, Node.js tooling, and pure JavaScript browser requirements all play nicely together.

The AI helped navigate the compatibility shimming, but this required back-and-forth, debugging context, and engineering judgment about which solutions made sense.

## What I Brought to the Table

This is the part that matters: **the AI was a force multiplier, not a replacement**. I brought:

- **Domain Knowledge**: Knowing that TypeScript would be valuable, understanding browser extension architecture
- **Quality Standards**: Demanding actual 100% coverage, not accepting claims at face value
- **Testing Discipline**: Actually running the extension and catching failures
- **DevOps Expertise**: Setting up CI/CD with linting, testing, code coverage, and proper branch protection

******![](/images/posts/blogger-aa80c64c4f.png)[](/images/posts/blogger-aa80c64c4f.png)****  
**

Without these, the project would have been:

- Written in a language I didn't care about learning
- Reported as having test coverage it didn't have
- Non-functional in actual browser use
- Lacking any deployment gates or quality controls

## The Honest Assessment

### What AI Did Well:

- Rapid scaffolding and boilerplate generation for a browser extension
- Pivoting between languages without rewriting from scratch
- Suggesting patterns and approaches I could evaluate
- Explaining TypeScript concepts as we worked
- Iterating toward 100% coverage once I insisted on it

### Where I Had to Course-Correct:

- Verifying claimed test coverage vs actual coverage
- Debugging the runtime environment mismatches
- Making architectural decisions about structure
- Determining what "good enough" looked like
- Setting up CI/CD and branch protection policies

******![](/images/posts/blogger-accba3f335.png)[](/images/posts/blogger-accba3f335.png)****  
**

## The Takeaway

AI-assisted development works when:

1. **The engineer sets clear requirements** (not vague "make me a thing")
2. **Claims are verified** (trust, but verify - actually, just verify)
3. **The engineer maintains quality standards** (100% means 100%, not 57%)
4. **Real-world testing happens** (if it doesn't run, it doesn't ship)
5. **The AI is a tool, not a decision-maker**

This isn't "AI replaces developers." This is "experienced engineer uses modern tooling to accelerate development while maintaining professional standards."

And yeah, I learned TypeScript in the process. Turns out it's not mystical knowledge - it's just static typing for JavaScript. If you've done C#, Java, or any other statically-typed language, you'll pick it up in an afternoon.

******![](/images/posts/blogger-889a02d879.png)[](/images/posts/blogger-889a02d879.png)****  
**

## The Result

- Fully functional browser extension: ✅
- 100% test coverage (actual, not claimed): ✅
- TypeScript experience: ✅
- CI/CD pipeline with quality gates: ✅
- Published to Edge Extension store: ✅
- Time investment: ~10 hours

Not bad for a language I "didn't have experience with."

---

******![](/images/posts/blogger-cc1b86aee5.png)[](/images/posts/blogger-cc1b86aee5.png)****  
**

You can check out the source at [github.com/grimlor/youtube-videos-tab-extension](https://github.com/grimlor/youtube-videos-tab-extension). Feel free to judge my TypeScript - I've had it for about 10 hours now.
