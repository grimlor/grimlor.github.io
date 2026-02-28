---
title: "Frustrating error #267: 'Only one &lt;configSections&gt; element allowed per config file and if present must be the first child of the root &lt;configuration&gt; element.'"
date: 2011-03-17
type: post
tags: ["ASP.NET"]
---

# Frustrating error #267: "Only one &lt;configSections&gt; element allowed per config file and if present must be the first child of the root &lt;configuration&gt; element."

I recently experienced this issue and struggled to find a solution. After reading through the many blogs and forums a [Google search returned](http://www.google.com/webhp?sourceid=chrome-instant&ie=UTF-8&ion=1&nord=1#sclient=psy&hl=en&nord=1&site=webhp&q=only+one+configsections+element+allowed+it+must+be+the+first+child&aq=1v&aqi=g1g-v1g-o1&aql=&oq=&pbx=1&bav=on.2,or.&fp=369c8973645261b8&ion=1), I was fortunate enough to read the [linked post](http://marlongrech.wordpress.com/2010/02/25/problem-configuration-system-failed-to-initialize/) which made the answer built into the error obvious. Move the <configSections> block to the top of the blocks inside of <configuration>. Just re-sharing the knowledge so the answer is more broadly known and findable.
