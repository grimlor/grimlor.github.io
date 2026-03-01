---
title: "Akka Serverless - The Future of Software, Part 3"
date: 2021-11-12
tags: ["Lightbend", "TheFuture", "Serverless", "Akka", "AkkaServerless"]
series: ["Akka Serverless"]
series_order: 3
slug: "akka-serverless-the-future-of-software-part-3"
description: "Well, now that you mention it, I did about a month of sleeping since my last post. Thanks for the nudge awake. In that interregnum we at..."
aliases:
  - "/2021/11/akka-serverless-future-of-software-part.html"
---

##  Ahem! Did You Fall Asleep?

Well, now that you mention it, I did about a month of sleeping since my last post. Thanks for the nudge awake. In that interregnum we at [Lightbend](https://www.lightbend.com/) were busy adding features to [Akka Serverless](https://www.lightbend.com/akka-serverless) to get it ready to emerge from [open beta](https://www.lightbend.com/blog/introducing-akka-serverless-open-beta). 

Oh, no! I just realized that I never told you that you can skip the queue (and my blog) to get right to [playing with what I think is the Next Big Thing™️](https://console.akkaserverless.com/p/register).

## Now Where Was I?
Ah, yes, I'd introduced you to the [Reactive Principles](https://principles.reactive.foundation/) and set the stage for talking about building message-driven software. If that's a puzzled look I'm seeing on your face, [go back and see my previous post](https://www.jackpines.info/2021/10/akka-serverless-future-of-software-part_11.html) to catch up.

Let me sum it up:

1. You want always-responsive software so that users don't wander off when they see a  
  
![SQUIRREL](/images/posts/Squirrel.gif)[](/images/posts/Squirrel.gif)  

2. Always-responsive software comes in the form of being both resilient to failure and...
3. ...elastic to meet demands, both growing to handle high load and shrinking when those demands pass so you aren't always paying for maximum potential.
4. The means by which that value of responsiveness is enabled is by making your software message-driven.

## Treat Yourself to a Mocha

Let's stroll over to the neighborhood coffee shop and chat about what being message-driven means. We deal with messages all the time. For example, when we walked into the coffee shop, the opening of the door triggered a chime to notify the staff that someone had come in (or left) the shop, saving us from having to shout out to get someone's attention from the back. Shouting or chiming, we send a message and they attend to that message or the message goes unheeded. 

In software, dropped messages happen all the time, especially in this "always" connected world, and we end up writing retry logic or setting up alerts to a potential issue for paging someone. However, we want resiliency because an unheeded customer walks back out that chiming door to find coffee elsewhere. 

So, what do we do? At minimum, we could add an Ack(nowledgement). There's a fast food joint that does that very effectively by having [every employee greet new customers](https://www.youtube.com/watch?v=M8fdK63m9eA). For us, that'd be like every instance of our service acknowledging every request—definitely overkill.

To be Ack'd, though, we'd have to be noticed. At our corner coffee shop, while they serve up the best mochas around, they only staff up during rush periods in an effort to be elastic. Otherwise, they only have one barista handling all aspects of the store. Our barista was in the back when we entered and didn't hear the chime. We know this barista is no slacker so we waited while another customer walked through the door, queuing up behind us. This time, the barista heard the chime and came up front, greeting us both. We got our Ack and are ready to make an order!

## Let's Chew This Over

One of my favorite things about the mocha here is that they throw in a biscotti. Oh, you meant what did we learn!

We just saw a few components of being message-driven. First, any time we work with another person, we do so by either asking them or telling them some kind of message, commonly referred to as a request or a command, accordingly. Next, we recognize that we can't expect that request or command to be fulfilled unless we know that the message has been received. Finally, in an ordered world we need to make sure that requests and commands, collectively referred to as messages, are handled in the order they're received. This is even true if we're able to process messages in parallel. 

For example, let's say that just as our barista was taking the other customer's order, another barista came on shift. We wouldn't expect that barista to wait to make the order of the customer behind us. Rather, they'd start making our order while the barista taking the order makes the other customer's order. Ours is a more complicated coffee order so comes out after the other customer's coffee. However, order was maintained even while the coffees were crafted in parallel.

There is one more thing to add, which you've seen before, to finish our message-driven paradigm. When you place an order, a ticket is printed up. If it's just the one barista and you're the sole customer, that ticket may hit the trash pretty quickly. However, it's likely you've seen them carry it over with them to the espresso machine to be sure they're making your order right. When the second barista enters the scene, they start pulling the tickets right off the printer, in effect passing your message on to them to fulfill. 

## The Actor Formally Known As
![Prince](/images/posts/Prince.gif)[](/images/posts/Prince.gif)  

I said Actor, not Singer, and “formally”, not “formerly”. **sigh**

For every system, such as this mocha production line, there are actors in the system, in this case the customer and the baristas. Those actors are passing messages to each other and they leverage a system to make sure coffee order tickets are processed in order and don't get dropped on the floor.

The stage is now set to finally introduce you to Akka, the underpinning technology to Akka Serverless. There are many coordinating components to [the Akka Platform](https://www.lightbend.com/akka-platform) but I want to focus on the Actor.

[In my next post.](http://www.jackpines.info/2022/01/were-all-actors-on-stage-of-life.html) 😎
