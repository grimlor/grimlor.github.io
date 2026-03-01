---
title: "Are you a Considerate Coder?"
date: 2010-05-04
tags: ["considerate coding", "best practices", "documentation"]
slug: "are-you-a-considerate-coder"
description: "**"
aliases:
  - "/2010/05/are-you-considerate-coder.html"
---

**  

Documentation is for dummies and "those" types of people!**  

Ok, so you are hot stuff when it comes to developing software and/or web applications. No one can possibly keep up with your "mad skillz", right? I know that I've heard that in my office a time or two. However, are you a considerate coder? "Oh, not another documentation Nazi!" I hear you saying. Maybe I am and maybe I'm not but let's explore this both from an altruistic and a selfish point of view.  

**  

Why do you care so much?**  

So call me selfish but I really like to understand the code I'm having to maintain. Oh, I hear you saying that your code is self-documenting. Then call me stupid, if that makes you feel better. It may be obvious to you what s = x != 0.0 ? Math.Sin(x)/x : 1.0; does but it can take me a minute to figure it out. That's a minute I could have back in my life if you would have had a one line comment above it saying "Approach limit; Test x to prevent divide by zero error". Ok, so that's a simple example but I've seen the ?: operator used in much more convoluted ways (in my own code) and that only alludes to the complexity that a code block can have. So, you think your code is easy to read because you named the variables well? Kudos to you if you do but I've seen a lot of code written by those less considerate than yourself which use the shortest variable names possible, as if Intellisense and auto-complete hadn't been invented yet.  

**  

And why should I care?**  

So, enough haranguing on the fact that code isn't easily digested by eyeballs. Let's revisit your statement that no one else on your team can keep up with your coding ability. That very statement underscores the need to document your code. It isn't just your grandmother's advice that you always wear clean shorts that has merit. Always document your code for the day that you end up unintentionally soiling those clean shorts due to the proverbial bus accident.  

One final appeal to the innate, selfish nature of all animals, even the human species. You'll look good if your boss can understand what your code was supposed to do. And in the event that the code doesn't do exactly what you expected, it'll be far easier to find the spot where it's most likely needing special attention and that'll make your boss happy, too.
