---
title: "Akka Serverless - The Future of Software, Part 2"
date: 2021-10-11
tags: ["Lightbend", "TheFuture", "Serverless", "Akka", "AkkaServerless"]
series: ["Akka Serverless"]
series_order: 2
slug: "akka-serverless-the-future-of-software-part-2"
description: "[When last we met](/posts/akka-serverless-the-future-of-software-part-1/), Dear Reader, I was leading into why I think [Akka..."
aliases:
  - "/2021/10/akka-serverless-future-of-software-part_11.html"
---

## You Were Saying?

[When last we met](/posts/akka-serverless-the-future-of-software-part-1/), Dear Reader, I was leading into why I think [Akka Serverless](https://www.lightbend.com/akka-serverless) is the future of software, at least any software built to run in the cloud, by giving you a little of my back story. Today I'm going to talk about the foundations upon which Akka Serverless is built. To do that, I have to present a few...

## Definitions
**[reactive](https://www.merriam-webster.com/dictionary/reactive)** ****[adjective](https://www.merriam-webster.com/dictionary/adjective)

re·​ac·​tive | \ rē-ˈak-tiv  \

**Definition of *reactive***

**1 : **of, relating to, or marked by [reaction](https://www.merriam-webster.com/dictionary/reaction) or [reactance](https://www.merriam-webster.com/dictionary/reactance)

**2  a : **readily responsive to a stimulus

**    b	: **occurring as a result of stress or emotional upset

*reactive* depression  

**[manifesto](https://www.merriam-webster.com/dictionary/manifesto)** ****[noun](https://www.merriam-webster.com/dictionary/noun)

man·​i·​fes·​to | \ ˌma-nə-ˈfe-(ˌ)stō  \

*plural ***manifestos*** or ***manifestoes**

**Essential Meaning of *manifesto***

**: **a written statement that describes the policies, goals, and opinions of a person or group

The group's *manifesto* focused on helping the poor and stopping violence.

a political party's *manifesto*  
**[The Reactive Manifesto](https://www.reactivemanifesto.org/) ******[document](https://www.merriam-webster.com/dictionary/document)

**Value Proposition of *The Reactive Manifesto***

: In today's distributed compute environment, where services rely on other services, we are building systems which rely on unreliable partners. Even in the best conditions where everything is built correctly, outages happen. Steps must be taken to deal with outages with the greatest of integrity and availability, Certain patterns have emerged that enables this in a fashion that is also easy to reason over as it models the interactions we have on a human level with each other.

  

## Ok, Class. Your Reading Assignment Is...

I really highly recommend you scooch on over to [The Reactive Manifesto](https://www.reactivemanifesto.org/), and the more thorough treatise, [The Reactive Principles](https://principles.reactive.foundation/), and then come back here. Go on. I'll wait.

Hmm.... I'm betting you didn't go or, like me, you now have 2 more browser tabs open in your endless array. I'll see if I can do a [TL;DR](https://www.merriam-webster.com/dictionary/TL%3BDR) version here.

First off, the ideas around the Reactive Principles aren't new. The Reactive Manifesto was something I tripped over in my design pattern studies back in the 20-teens. [Jonas Bonér](https://twitter.com/jboner), [Dave Farley](https://twitter.com/davefarley77), [Roland Kuhn](https://twitter.com/rolandkuhn), and [Martin Thompson](https://twitter.com/mjpt777) had collaborated to bring together the ideas around 2014 but the concepts preceded them by decades. They just hadn't come to be something we business application developers really needed to know (despite how much I wanted my peers and managers to think it was necessary). Then came The Cloud.

![Nobody understands the cloud!](/images/posts/nobody-understands-the-cloud.png)[](/images/posts/nobody-understands-the-cloud.png)  

Suddenly, in my circles anyway, everyone wanted to put their .NET applications up in Azure. Yet, no one was building applications to run effectively in Azure -- even inside Microsoft. I don't mean any slight to the engineers I was working with. It's simply that most folks weren't so forward looking, or were not as exposed to distributed computing, as Jonas, et al, were.

## K.I.S.S.S.

First, I have to say that I'm standing on the shoulders of giants, here. Further, if I can't describe something simply, I probably don't understand what I'm talking about. So, when I suggest that I'm going to Keep It Simple, it's so that my Silly Self can actually explain it. And, honestly, this software design pattern is extremely simple, conceptually speaking. 

In a distributed system, which aptly describes all aspects of The Cloud, [things you have absolutely no control over break](https://cacm.acm.org/magazines/2020/2/242334-everything-fails-all-the-time/fulltext). However, that's no excuse to let the end-user suffer, either catastrophically or silently. With a minimum of 3 core concepts, you reach the 4th needed to be a Reactive System, which is always responsive software. ![Being elastic & resilient results in responsive software, all enabled by being message-driven](/images/posts/reactive-traits.png)[](/images/posts/reactive-traits.png)  

Responsiveness is simply defined as always responding in a timely manner if at all possible. As noted above, that means you need your system to be resilient to failure. 

You also need to have your system responsive under varying loads.  You could over-prepare, something many corporations did in their own data-centers regularly, by running a system, understanding what it takes to run it, combine that with trends from the past, and then building out infrastructure to match the highest load, plus a little for growth. Unfortunately, that often means inactive servers just so you don't hit a critical tipping point that crashes your application. When building for The Cloud, though, you can think more elastically, right-sizing your resources so that they're not too big, not too small, scaled up or down just in time to be just right.

## Parenting as a Software Analogue

None of that is possible, though, without the fundamental underpinning of being message-driven. If you work from a task list, an email inbox, a ticketing system, etc., you, Dear Reader, are message-driven. However, most developers are used to thinking about method calls. Whether because they're writing object-oriented code making internal calls or making web API calls, these have an inherent flaw any parent with a young child knows. You have to wait and monitor for the expected result, handling exceptions as they arise. It ties up the code, like that parent, until that operation is complete before another can occur. Even the exceptionally brilliant parent, skilled at multitasking, will run out of threads if they are often checking on, responding to, or redirecting the efforts of their child. 

The answer to all this is to introduce asynchrony. That brings me back to your task list. As a kid, you may have had a chores list. That allowed you to do your chores, unsupervised, and let your parent check on the status of your efforts when they had the bandwidth. They still may have had to take corrective action but they have achieved a higher level of productivity. 

## Missed It By *That* Much

With both software and kids, this takes some maturity but by the time I was maturing from a naïve coder to a software engineer, the .NET Framework had matured to introduce the [Task Parallel Library giving us Async/Await in v4.5, released in 2012](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/). While Java's Futures preceded that by quite some time, CompletableFutures in Java 8 showed up in 2014. Until then, there were [a lot of shortcomings that made truly asynchronous programming difficult](https://blog.knoldus.com/future-vs-completablefuture-1/). There are also some distinct differences, notably around thread use, between how .NET and Java got the job done but they both have the same flaw. They both still, inevitably, await the result of the call. ![](/images/posts/skeleton-still-waiting-for-a-callback.jpeg)[](/images/posts/skeleton-still-waiting-for-a-callback.jpeg)  

Because it is possible to have a call never return, it is possible to have an unresponsive application even when built using asynchronous patterns. Ever been that parent (or the child here) who, all else done, just can't get their kid to pick up their toys? Yeah, that. You either have some timeout and error-handling (pick up the toys yourself, perhaps) or you both pass out before the battle of wills resolves.

There is another step in maturity needed, which brings me back to being message-driven. I took a lot of time to get to that because it is a simple, but profound paradigm shift. However, it is one that should be very familiar to you.

## Stay Tuned

Did I say TL;DR version? If you're new here, you've now learned what those who know me already knew. "Long" is my middle name. This has gotten long, though still a lot shorter than either of the two sites on defining Reactive systems. So, [I will pick up on my next post](/posts/akka-serverless-the-future-of-software-part-3/) with what being message-driven means and how an old pattern became new again.
