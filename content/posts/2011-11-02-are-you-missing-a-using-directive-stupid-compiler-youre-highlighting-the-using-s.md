---
title: "Are you missing a using directive? Stupid compiler, you're highlighting the using statement!"
date: 2011-11-02
tags: [".NET frameworks", "deceptive errors"]
slug: "are-you-missing-a-using-directive-stupid-compiler-youre-highlighting-the-using-s"
description: "So, here I am, bopping along after adding my company's ([InCharge Institute](http://www.incharge.org/)) framework library and referencing it in my project...."
aliases:
  - "/2011/11/are-you-missing-using-directive-stupid.html"
---

So, here I am, bopping along after adding my company's ([InCharge Institute](http://www.incharge.org/)) framework library and referencing it in my project. I've added a using statement and completed writing the code, which [ReSharper](http://www.jetbrains.com/resharper/) blesses and gives no syntax errors. And yet, I get a compiler error:  

> 
The type or namespace name 'InCharge' could not be found (are you missing a using directive or an assembly reference?)
Double-clicking the error takes me straight away to the using line that it claims might be missing. Madness! The answer, as is typical, is simple if not obvious. My project was automatically created with the target framework of .NET Framework 4 Client Profile. You can research what that's all about but it clearly ignores references to other projects. Switching that to simply .NET Framework 4 in the project's properties resolved the issue without any further fuss.
