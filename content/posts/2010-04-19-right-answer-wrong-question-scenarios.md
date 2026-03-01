---
title: "Right Answer Wrong Question Scenarios"
date: 2010-04-19
tags: ["OLAP perspective", "business intelligence", "RAWQ", "BI"]
slug: "right-answer-wrong-question-scenarios"
description: "As I [mentioned in an earlier post](http://bidevadventures.blogspot.com/2010/04/one-method-to-get-combined-dataset-of.html), one way that we provide..."
aliases:
  - "/2010/04/right-answer-wrong-question-scenarios.html"
---

As I [mentioned in an earlier post](http://bidevadventures.blogspot.com/2010/04/one-method-to-get-combined-dataset-of.html), one way that we provide intelligence to the data, resulting in information, is to prevent Right Answer Wrong Question (RAWQ) scenarios.  A RAWQ scenario occurs when data is returned which seems sane on first glance but upon inspection it is found that the numbers don't match reality.  This happens in SQL with improper joins but is typically caught quickly by the developer.  However, when you expose your data for the public to consume in cubes, RAWQ happens altogether too frequently.

This is best shown via an example.  Consider a Public Libary cube which has three date dimensions, Date Checked In, Date Checked Out, and Date Held.  Similarly, it has three people dimensions, one for Held By, Checked Out By, and Checked In By.  (Ok, so the library doesn't know who checks the book in.  Go with it. ;-)  A typical MDX query might look like this:

```
SELECT 
{ 
   [Measures].[Count Books] 
} ON COLUMNS, 
NON EMPTY { 
   [Checked Out By].[Checked Out By].Members 
} ON ROWS 
FROM 
   [Books] 
WHERE 
( 
   [Date Held].[Date].&[2010-04-19T00:00:00] 
) 

```

Do you see the wrong question?  You will get an answer and it won't be the wrong one but you asked the cube to show you a count of books checked out by all patrons which was held on April 19th, 2010.  It's a good question, even a potentially useful one, but it would be rare that the library needs to know this information.  More likely they'd want to know either who held how many books on a date or who checked out how many books on a date.

We implemented a solution to this by creating [Perspectives for cubes](http://technet.microsoft.com/en-us/library/ms175338.aspx) where such RAWQ scenarios exist.  Doing so where each perspective has matching dimensions, i.e. a [Checked Out Books] perspective with the  [Checked Out By] dimension matching the [Date Checked Out] dimension, while hiding the [Books] cube from public consuption.  Only developers have access to the "raw" [Books] cube.
