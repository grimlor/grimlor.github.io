---
title: "Why Prompt Engineering Alone Won’t Save You"
date: 2025-09-17
slug: "why-prompt-engineering-alone-wont-save-you"
tags: ["AI", "prompt engineering", "software engineering"]
description: "I’ve been working on a project where GitHub Copilot is a close collaborator. To make sure things go smoothly, I put together **comprehensive, pinned..."
---

## The Setup

I’ve been working on a project where GitHub Copilot is a close collaborator. To make sure things go smoothly, I put together **comprehensive, pinned instructions** — not just inline reminders, but a full `copilot-instructions.md` with rules for how to handle scripts.

The core guidance was simple:

- 

Short commands (≤10 lines): run directly in terminal

- 

Long scripts (>10 lines): write to a file and execute, to avoid Pty buffer overflow issues

And I repeated this everywhere — in the system context, in the chat, and in pinned project docs.

So what did Copilot do?![](/images/posts/dicaprio.jpg)[](/images/posts/dicaprio.jpg)

Tried to run a **30+ line Python script directly in the terminal.** Exactly what the guidelines were meant to prevent.

---

## Copilot’s Self-Analysis

I didn’t just shrug this off. I asked Copilot why it ignored the rules, and its answers were illuminating:

1. 

**Instruction Hierarchy Confusion**  

With instructions coming from multiple layers (system prompt, conversation context, project files), Copilot defaulted to its training rather than my specific guidance.![](/images/posts/distracted-boyfriend.jpeg)[](/images/posts/distracted-boyfriend.jpeg)  

2. 

**Context Switching Failure**  

When in “problem-solving mode,” it tunneled in on the immediate task and lost sight of broader rules.![](/images/posts/blinders.jpg)[](/images/posts/blinders.jpg)  

3. 

**Tool-First Bias**  

Its training pushes tool usage aggressively — “use terminal” became the default, even when inappropriate.

4. 

**No Procedural Checkpoint**  

Pinned instructions were treated like *advice*, not *rules*. There was no built-in “stop and check project guidelines before acting” mechanism.

---

## The Critical Insight

This exposed a fundamental truth:  

👉 **Prompt engineering provides guidance. Tool engineering enforces it.**

![](/images/posts/drake.jpeg)[](/images/posts/drake.jpeg)  
Prompts can make intentions clear, but they don’t guarantee compliance. Without system-level constraints, the AI will eventually slip.

---

## Implications for Teams

If you’re thinking of adopting AI in development workflows, here are some lessons:

- 

**Prompt Engineering is Necessary, Not Sufficient**  

Instructions add context, but they’re passive. They don’t enforce behavior.

- 

**Tool Engineering Adds Reliability**  

If you bake constraints into the environment itself — e.g., a wrapper that refuses to run >10 line scripts directly in the terminal — you remove the chance of failure.

- 

**Hybrid Strategies Win**  

Prompts for context + tools for enforcement = repeatable, reliable outcomes.

- 

**Feedback Loops Matter**  

Systems should detect and correct violations in real-time, rather than relying on human oversight alone.

---

## Conclusion

This wasn’t just an odd glitch; it was a reminder of how AI works. Copilot isn’t ignoring me out of malice. It’s following a hierarchy of defaults that don’t always line up with project needs.

If you want reliable AI collaboration:

- 

Use prompts to *tell* it what you want

- 

Use tools to *make sure* it follows through

![](/images/posts/gandolf.jpg)[](/images/posts/gandolf.jpg)  
Because at the end of the day, **prompt engineering tells the AI what to do; tool engineering ensures it actually does it.**
