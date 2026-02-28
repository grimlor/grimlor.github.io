---
title: "StackTrace Tool"
date: 2016-01-28
type: post
tags: ["Universal Windows Platform", "UWP"]
---

# StackTrace Tool

Wow! Over a year since I last posted. I really need to find the overlap of time and motivation to bring some value to the few who stop by here. Here's something. I created this and hope you might find it useful. Go [check it out](https://github.com/grimlor/StackTraceTool) on my [GitHub repo](https://github.com/grimlor).  

  

# 
[StackTraceTool](https://github.com/grimlor/StackTraceTool)

Collects tools useful for working with Windows stack trace.

## 
Inspiration & Initial Release

I often have to debug process failures where something unexpected happens. Even when the unexpected is handled through logging, the logged stack trace gets mangled a bit due to the newline character getting turned into escaped text, eg. '\r\n'. So, I'd often stare at this in the tiny message area in Event Viewer or the enterprise's logging mechanism and end up copy/pasting into my text editor of choice and replacing all the '\r\n's manually with actual newlines. My motto has always been, "Don't do any repetitive work if you can automate it."
  

It'd be easy enough to create a Python script, a console app, or a simple WinForms application. However, I've never built a Universal Windows Platform app and this was the perfect impetus to spin one up. Currently, the prompt is the only feature. However, I welcome input on ways this tool may be improved.
