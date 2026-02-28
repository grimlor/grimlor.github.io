---
title: "One method to get a combined dataset of OLAP data"
date: 2010-04-19
type: post
tags: ["linked server", "SSAS", "BI", "openquery", "data access methods", "business intelligence"]
---

# One method to get a combined dataset of OLAP data

When I started with this team, about 3 years ago, they had just completed a business intelligence solution that allowed any (internal) user with a web browser to pull data from several SQL Server Analysis Services (SSAS) cubes.  As I [mentioned in my introductory post](http://bidevadventures.blogspot.com/2010/04/introduction-of-this-blog-and-its.html), it seems that most BI is done in Excel one cube at a time.  Our [STAR Award](http://www.thesspa.com/starawards/best_practices.asp)-winning solution, while still representing only one cube at a time, did not have this limitation.  Further, it has allowed us to provide views into the data which help to prevent [Right Answer Wrong Question scenarios](http://bidevadventures.blogspot.com/2010/04/right-answer-wrong-question-scenarios.html).  

  

Once our users got used to having such easy access to their data, they suddenly wanted to mix up the data in the different cubes to see new, potential relationships.  They were already receiving some of this data in various reports and some rolled up in a summary report, all created using SQL.  However, if the SQL developer didn't use the appropriate joins and where clauses to match the filters and slicers that the user selected on our BI solution, the numbers wouldn't match up.  Further, the old SQL reports ran much slower than the BI solution.  Thus, it was decided that we could get speedier, more consistent results if we started using the OLAP cubes for our reports.  (Doing so sealed the deal on the [STAR Award](http://www.thesspa.com/starawards/best_practices.asp).)  However, doing so is not so easy in Reporting Services.  You have to get each cube's data separately and representing them on the report can get messy and is very design intensive due to the usage of several stacked tables.  It would be really nice if we could use one matrix (we were on SQL Server 2005 at the time) to dump all of the different cube's data, with the only commonality being that they all represented the same date range.  

  

My coworker investigated and we decided [to implement a linked server](http://support.microsoft.com/kb/218592) in SQL to SSAS.  With this, we could create a stored procedure that collated MDX query results against multiple cubes, delivering a table to the SSRS report and speeding up the resulting report.   

  

This has functioned well for us for 2+ years.  However, recently we have been creating web applications, where query time is quickly noticed by the end-user, and have found that this has added overhead to the queries which we want to eliminate.  More on that in a future post.
