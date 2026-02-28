---
title: "My Adventure in Reinventing the Wheel"
date: 2010-06-10
type: post
tags: ["data factory", "design patterns", "design", "datafactories", "data access methods", "data factories", "adomd stored procedures"]
---

# My Adventure in Reinventing the Wheel

**Why I Needed a New Wheel**  

Let me begin by saying that this is going to be a long article. It took me months to develop what I'm presenting and weeks to write the article. Hopefully, it takes you less time to see the value in my sharing the pains I took and my results.  

  

In an [earlier article](http://bidevadventures.blogspot.com/2010/04/one-method-to-get-combined-dataset-of.html), I mentioned that my team had settled on using a linked server methodology for accessing OLAP data via our SQL instance. While this does provide benefits, it isn't the quickest path to the data. Further, as I began developing more web applications, such as interactive data divers and operational dashboards, rather than Reporting Services reports, the impact of this double hop to the data became more noticeable. It was time to find a more direct path to the data.  

  

My initial goal was to create a way to reference parameterized MDX queries with expected outputs of either a strongly typed DataTable or a DataSet as my use of these queries was always for presenting a table or graphical representation of key performance indicators.  

  

**Found a Unicycle**  

When I began this voyage, my skills were strong but my knowledge was sorely lacking. Thus, I began with the simple belief that I needed to jump right into ADOMD.NET, Microsoft's framework for working with Microsoft SQL Server Analysis Services. Before jumping into the deep end, I did what I've always done and sought out swim fins. Usually, I come up short on this as not many developers have documented their travails through these waters. This time, however, I found [Sacha Tomey's](http://blogs.adatis.co.uk/blogs/sachatomey/default.aspx) blog entry, [MDX Stored Procedures](http://blogs.adatis.co.uk/blogs/sachatomey/archive/2007/08/17/mdx-stored-procedures-part-1.aspx) where he talks about a way to use a text file as a kind of stored procedure for SSAS, storing a parameterized MDX query and ensuring that you get a (optionally strongly typed) DataTable or DataSet back from the query. It's an old post from 2007 so it was heartening to see that someone had preceded me on my quest. It's a pretty straightforward implementation and he released the full source code in his [Part 2](http://blogs.adatis.co.uk/blogs/sachatomey/archive/2007/11/20/mdx-stored-procedures-part-2.aspx) post.  

  

Sacha's class expects that you know what your SSAS server, database, and cube will be at design time. Now, I may not be very knowledgeable but one thing I know is that this is not easily maintainable code. You see, I have a development platform, a QC platform, and a production platform. While they all have databases with the same names, the server names differ. That's why we never hard code server names. Rather, we code using connection names which are stored in our web.config file as connection strings and each server has its own web.config file modified appropriately. So, the first thing I knew I had to do was modify the code to allow the use of connection strings.  

  

**My First Wheel Resembled Swiss Cheese**  

Ok, so my wheel was round but I started seeing holes in the implementation immediately. As I created this modified implementation of Sacha's work ([duly documenting from where I'd lifted the code](http://bidevadventures.blogspot.com/2010/05/are-you-considerate-coder.html)), I thought to myself how cool it would be if his MDXHelper class were actually two classes, one for the connection and one for the command, you know, like System.Data.SqlClient has. So, I created a namespace called MdxClient and two classes, MdxConnection and MdxCommand and started moving Sacha's methods into the appropriate classes. As I was tearing apart his constructors to do my bidding, I didn't bother to implement constructors for the MdxConnection involving a server/database pair and implemented my connection string version instead.  

  

About the time I finished with the MdxConnection and started moving on to the MdxCommand, I had an enlightened conversation with my mentor, a much more senior developer I talk to every blue moon. For the last year he's been telling me to study up on [design patterns](http://en.wikipedia.org/wiki/Design_Patterns). Today, this is required coursework in many CS degree programs but I'm only vaguely aware of design patterns myself. Until recently, I was just a spaghetti programmer in "get 'er done" mode at my boss's insistence. This time, however, I had the specific goal of "get 'er done...faster" as the web pages were not meeting the responsiveness that our customer's expected. And to make things faster I needed to make things better, not just clean and well-documented with a minimum of repeated code.  

  

My mentor floated out lots of ideas along with several pattern names, including Factory, Singleton, and Adapter. Two of those sounded familiar since I've used the SqlDataAdapter and seen, while investigating the DbConnection something called the DbProviderFactory. I really didn't know what each of these meant and, while my mentor tried to educate me in about an hour and a half, it was after midnight when this conversation began and excitement only carried me so far.  

  

(My mentor also said I shouldn't continue any development without creating the tests first, AKA test-driven development but I'll talk about that in another post.)  

  

**My Second Wheel Resembled Emmental**  

So, I went back to my desk the next day and started opening up the definitions of the SqlConnection and SqlCommand to see what they contained. I was sleepy and figured this was a good place to start just to see if what I was doing with MdxConnection and MdxCommand had a similar footprint. After all, one thing that stood out from my conversation the night before was that my "how cool would it be if" moment when I started on this journey had merit and now I knew why. If I could make them have the same functionality, then it wouldn't matter if today we wanted the application to connect to SSAS to get its data and tomorrow we wanted the same application to query SQL instead.  

  

While the footprints were similar, I found I had more work to do and got to doing it. It wasn't until the next day, after more sleep, that I realized that maybe the work I was doing wasn't achieving my goals. First off, it seemed like a lot of what I was doing was just reproducing functionality already in AdomdConnection and AdomdCommand. So, as I started deferring to their method implementations, I wondered if I was on the right track or not. It turned out that AdomdConnection implements a lot of the same methods as SqlConnection. That's because SqlConnection inherits from the abstract class DbConnection which implements the interface IDbConnection, the same interface which AdomdConnection implements. Similarly, SqlCommand inherits from DbCommand which implements IDbCommand, the same interface which AdomdCommand implements.  

  

Then it dawned on me. I'm reinventing the wheel. Maybe it was time to stop developing a solution to my problem and start designing one. What were those design patterns again?  

  

**Off to the Wheel Factory**  

Recalling the DbProviderFactory as I was taking my first stab at my own MdxClient, I decided to take the advice often given and avoid reinventing the wheel. What I discovered pretty quickly, with great disappointment, was that ADOMD.NET didn't support DbProviderFactory. [Robert Bouillon](http://robertbouillon.com/) wrote an article stating as much but with a [useful workaround](http://robertbouillon.com/2010/04/08/adomd-dbproviderfactory/), use OLE DB instead of ADOMD.NET.  

  

While a very cool solution for some needs, alas it didn't suit my purposes. The problem is that OLAP is good about returning System.String objects for text when using ExecuteReader() but for numbers it returns System.Object objects. I wanted strongly typed objects so that I could perform calculations without having to perform casts first. From Sacha's article, I'd already seen that a solution to this was to use ExecuteXmlReader() and then use the details in the XML to create a strongly typed DataTable. Unfortunately, OLE DB doesn't support. I'll admit here that frustration was setting in.  

  

**Forget Wheels! Time to Build a Better Mouse Trap!**  

Ok, so I decided that it was time to stop hunting for wheels or attempting to invent my own. Instead, it was time to take existing ideas and designed something that suited my own vision. I was still sold on the [Abstract Factory Pattern](http://en.wikipedia.org/wiki/Abstract_factory_pattern) as it would allow me to switch between different data access methodologies as long as they all provided the same functionality, most importantly the support for parameterized scripts (aka stored procedures) and strongly typed output. My solution, the IDataFactory interface with concrete implementations of AdomdClientDataFactory and SqlClientDataFactory. The heavily commented implementation is available on [CodePlex](http://datafactories.codeplex.com/).
