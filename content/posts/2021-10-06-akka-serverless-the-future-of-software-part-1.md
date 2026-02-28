---
title: "Akka Serverless - The Future of Software, Part 1"
date: 2021-10-06
tags: ["Lightbend", "TheFuture", "Serverless", "Akka", "AkkaServerless"]
series: ["Akka Serverless"]
series_order: 1
slug: "akka-serverless-the-future-of-software-part-1"
description: "For those just joining the game, you might peruse my posts and see that I have dabbled as a software developer/engineer in multiple languages. I got my..."
---

## Some History (Mine)

For those just joining the game, you might peruse my posts and see that I have dabbled as a software developer/engineer in multiple languages. I got my start with BASIC back when Saint Jobs was working closely with The Woz to bring us the Apple II. I moved onto Fortran, failed to understand pointers with Pascal, helped build some stuff with VisualBasic, built production code with C#, came to understand pointers and data structures with C++, learned a bit of Java, enjoyed data munging with Python, and finally matured to the Software Engineer (SWE) title building enterprise services with C#. Then, wanting to see what life as a SWE was like outside of Microsoft, I returned to Java and, well, hated it. 

The tooling I had to use, IntelliJ IDEA, wasn't as familiar, thus not as good to me, as Visual Studio. It wasn't bad, and better than Eclipse, just really unfamiliar. The ways to handle asynchronous communication in Java were much harder to reason about, for me, than with C#. There was more need with Java to concern myself about threads than there had been for C#. The libraries were all foreign to me so I felt like a burden on my team although I'd already gained the Senior title. I understood architecture and patterns but I just didn't know the tools I was given.

My first professional foray into Java had two big things going for it, though. I had a great team who never made me feel like a burden and actually showed that they valued my expertise. It was great to work with a truly inclusive team. The other thing going for it was that they'd selected a toolbox that alleviated my difficulties with reasoning about asynchrony and dealing with threads. That toolbox was from a small, open source company named [Lightbend](https://www.lightbend.com/) and the primary tool was [Akka Platform](https://www.lightbend.com/akka-platform).

## Calamity Strikes

Unfortunately, roughly 3 months after joining that team, the company decided they would shutter the offices here in the PNW. It was calamitous in that I was really enjoying working with these folks, the project we were collaborating on, and I was getting the hang of the tools, language, and loving Akka.

Fortunately, I was welcomed back to Microsoft and got to work on a notable team building services underpinning all of Microsoft Store. My responsibilities were notable and the work was impactful. The team was not inclusive, though, and I really missed that. While there were some senior members of the team who were always approachable and helpful, others made a habit of being the opposite. Further, some of the younger folks, around the same age as my last teammates (I'm a young grandpa, for what it's worth), were expressly dismissive and exclusive in their interactions.

## Good Things Come

Thing is, I'm really not hard to get along with and that role prior to my return to Microsoft, the one that went sideways 3 months in, resulted in some friendships, too. A few of the folks I had worked with landed roles at another company in the area and, while I was working back at Microsoft, they were building trust in their management. That trust resulted in the opening of a role for me to join them a year after I'd rejoined Microsoft. And to add to my luck, they were building services for their SAAS product using Akka!

I was building a multi-tenant, streaming service leveraging Kafka using [Akka Streams](https://developer.lightbend.com/docs/telemetry/current/instrumentations/akka-streams/akka-streams.html) [Alpakka](https://doc.akka.io/docs/alpakka/current/). It was comprised of 3 microservices, each able to scale independently to handle the processing load as we moved customers from the overburdened, individual software instances onto this leaner, scalable solution. The scaling and resiliency were enabled via [Akka Clustering](https://doc.akka.io/docs/akka/current/typed/index-cluster.html) as the docker images were running in Kubernetes in AWS using [Akka Discovery's K8s API](https://doc.akka.io/docs/akka-management/current/discovery/kubernetes.html). It was a great opportunity to both dive deep into multiple technologies while mentoring a team of folks, most of whom this would be their first experience with most of it.

## Hello, Is It (Really) Me You're Looking For?

Have you heard this one? "Knock, knock." "Who's there?" "Lightbend." "Lightbend? No way!"

I was shocked to hear it myself. Here I'd found myself being really productive and actually enjoying Java despite my misgivings about it being less approachable than C#, all thanks to their flagship product Akka Platform. Lightbend is calling me to consider joining them as a Senior Solution Architect? It was true and that is what I did. 

As long-time readers of this blog will know, I had long ago discovered that I love learning and sharing what I learn. I had a real talent at communicating complex ideas at various levels and had started turning my journey from software development to mentoring and developer advocacy (DA), back before I got tapped to focus more on my craft and pivot to a Software Engineer role. (The differences between developer and SWE are hotly debated in the industry and a topic for a completely different post.) This blog was started to make the DA pivot only to languish as I focused on being a SWE.

However, as a Solution Architect/Technical Account Manager (my current title) at Lightbend, I get to turn my attention back to developer advocacy. Yay! As such, you should expect more of that kind of content to return (and less of the personal grousing about bad service).

## Stay Tuned

With that in mind, I'm starting this new series on a service from Lightbend, [currently in public beta](https://www.lightbend.com/blog/introducing-akka-serverless-open-beta), that I'm proud to be sharing with you. As the title of the article notes, that is [Akka Serverless](https://www.lightbend.com/akka-serverless) and I do believe it is the future of building software, at least the backends for highly connected, distributed, scalable, and resilient software. As always, I'm making this up on the fly so my plan is a little loose right now. However, for sure the next post will be a quick overview of the foundations that Akka Serverless is built upon, why they're important, and why Akka Serverless lets you stop worrying about them. You've heard of Platform as a Service, PAAS? I'll show you why I think of Akka Serverless as PAAS+, where the plus is that you don't have to worry about the complexities of actually leveraging the power of PAAS when building your truly cloud-first software, just your business domain and logic.

Oh! I almost forgot to mention that I also picked up a new language, thanks to Lightbend. I'm now building with [Scala](https://www.lightbend.com/scala-the-lingua-franca-of-scalable-systems). I'll be using Scala in some of my demos as it reads almost like pseudocode. However, [Akka Serverless supports a wide variety of programming languages](https://developer.lightbend.com/docs/akka-serverless/setting-up/index.html#_supported_languages), including full tutorials for Java and JavaScript. So, you never know what language I'll demo next.

As the heading says, [stay tuned](http://www.jackpines.info/2021/10/akka-serverless-future-of-software-part_11.html)!
