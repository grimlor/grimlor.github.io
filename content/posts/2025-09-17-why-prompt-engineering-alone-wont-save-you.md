---
title: "Why Prompt Engineering Alone Won’t Save You"
date: 2025-09-17
type: post
---

# Why Prompt Engineering Alone Won’t Save You

## The Setup

I’ve been working on a project where GitHub Copilot is a close collaborator. To make sure things go smoothly, I put together **comprehensive, pinned instructions** — not just inline reminders, but a full `copilot-instructions.md` with rules for how to handle scripts.

The core guidance was simple:

- 

Short commands (≤10 lines): run directly in terminal

- 

Long scripts (>10 lines): write to a file and execute, to avoid Pty buffer overflow issues

And I repeated this everywhere — in the system context, in the chat, and in pinned project docs.

So what did Copilot do?![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQuq7oz-EPrrvPoVlDIUE_f6Vyhn7aoBWgsoaGpK4q37zFBmTwp790d6_wHfgcadQ6g_yS6cPiacD32Nap2F3NTYaWWvWVgvhUQ-aPwpc6yu4iAu15bUULm8k6npRshFQeHQGZIZfj39ZuFNqyeKwtlAeeeJQNefF3lDc0ttzXGFXHVxKXKZf4n-qWn2Eu/s320/dicaprio.jpg)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQuq7oz-EPrrvPoVlDIUE_f6Vyhn7aoBWgsoaGpK4q37zFBmTwp790d6_wHfgcadQ6g_yS6cPiacD32Nap2F3NTYaWWvWVgvhUQ-aPwpc6yu4iAu15bUULm8k6npRshFQeHQGZIZfj39ZuFNqyeKwtlAeeeJQNefF3lDc0ttzXGFXHVxKXKZf4n-qWn2Eu/s1000/dicaprio.jpg)

Tried to run a **30+ line Python script directly in the terminal.** Exactly what the guidelines were meant to prevent.

---

## Copilot’s Self-Analysis

I didn’t just shrug this off. I asked Copilot why it ignored the rules, and its answers were illuminating:

1. 

**Instruction Hierarchy Confusion**  

With instructions coming from multiple layers (system prompt, conversation context, project files), Copilot defaulted to its training rather than my specific guidance.![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhm3nrPVGJDAhgMon_u6SS4L-1aX8PGHkxJ-sqYd5P5RC_LqN5aRc-HEs337feB72cGWzixw2Kh_VVqE88R8UxzSXlwmZS8LXmxpTbmvEvGBxW7mKMzEtCFKOoGox8dsgEii44HrqwYtmMChk8JD_OddJsEVE-F9eON6Gjx0z87YI0YnzgAYHh1UCArLCNR/s320/distracted%20boyfriend.jpeg)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhm3nrPVGJDAhgMon_u6SS4L-1aX8PGHkxJ-sqYd5P5RC_LqN5aRc-HEs337feB72cGWzixw2Kh_VVqE88R8UxzSXlwmZS8LXmxpTbmvEvGBxW7mKMzEtCFKOoGox8dsgEii44HrqwYtmMChk8JD_OddJsEVE-F9eON6Gjx0z87YI0YnzgAYHh1UCArLCNR/s750/distracted%20boyfriend.jpeg)  

2. 

**Context Switching Failure**  

When in “problem-solving mode,” it tunneled in on the immediate task and lost sight of broader rules.![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgWkObvOiV8-SKzVnVA_NgYBI6zfPjriP2_f7KWmXxbSL5z9jj9wXoNr6CT3hk_QldohEeZL0gC3-v18i1YwI_9lhSwT1GsICzJ4WjHEvdjLyCzq9sZK4onDEUDQZR7dK2LC1Di0UC2an3hQ6H6Gl3lqyjKoMeseh8fi-04neuv75QaMgwm1WEeBur8eoB-/s320/blinders.jpg)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgWkObvOiV8-SKzVnVA_NgYBI6zfPjriP2_f7KWmXxbSL5z9jj9wXoNr6CT3hk_QldohEeZL0gC3-v18i1YwI_9lhSwT1GsICzJ4WjHEvdjLyCzq9sZK4onDEUDQZR7dK2LC1Di0UC2an3hQ6H6Gl3lqyjKoMeseh8fi-04neuv75QaMgwm1WEeBur8eoB-/s683/blinders.jpg)  

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

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZPaKGrydEpstGhbG3kIbj-DJf6mWEV-Nb3nkKl29mFHGphKQ_hqTQB6dRPygEVLV-j4PSfFlsCWxalx0zMXodhK95uAL4bmjQwhGWi9AybxdCA3cTR3XjekiKwNC4zQ3J6FIe66NVUN_BThRhLrEViyE75OFAar7lQBGDnOTEdDpHju_Oe-rTxE-hrgrN/s320/drake.jpeg)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZPaKGrydEpstGhbG3kIbj-DJf6mWEV-Nb3nkKl29mFHGphKQ_hqTQB6dRPygEVLV-j4PSfFlsCWxalx0zMXodhK95uAL4bmjQwhGWi9AybxdCA3cTR3XjekiKwNC4zQ3J6FIe66NVUN_BThRhLrEViyE75OFAar7lQBGDnOTEdDpHju_Oe-rTxE-hrgrN/s500/drake.jpeg)  
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

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhM9YsgNaNa5QYb2zjiFlB1ZioOZcaDtC_9L_r35-YGSlg7dQE1igUFCvLjJhxxsjwIwa2rPyHwfXEwFZVIlGmKcJuojYsSZasNEsx0kiv23XL7_UatRqi9qkZ5mm4d_S_E0wcRJy8SJUIS-CXdn59iQiRmA9-UtQlX5_9JCnX_ku9ZSEqbPAvp4YYNlaYE/s320/gandolf.jpg)[](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhM9YsgNaNa5QYb2zjiFlB1ZioOZcaDtC_9L_r35-YGSlg7dQE1igUFCvLjJhxxsjwIwa2rPyHwfXEwFZVIlGmKcJuojYsSZasNEsx0kiv23XL7_UatRqi9qkZ5mm4d_S_E0wcRJy8SJUIS-CXdn59iQiRmA9-UtQlX5_9JCnX_ku9ZSEqbPAvp4YYNlaYE/s888/gandolf.jpg)  
Because at the end of the day, **prompt engineering tells the AI what to do; tool engineering ensures it actually does it.**
