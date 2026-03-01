---
title: "My First Hack at Functional Programming"
date: 2011-02-25
slug: "my-first-hack-at-functional-programming"
tags: ["functional programming", "learning"]
description: "I'm learning that with every new bug I learn something. If you've been keeping up, I use my [Symantec.DataFactories](http://symantec.datafactories/) to code..."
aliases:
  - "/2011/02/my-first-hack-at-functional-programming.html"
---

I'm learning that with every new bug I learn something. If you've been keeping up, I use my [Symantec.DataFactories](http://symantec.datafactories/) to code against SSAS OLAP cubes. It's very rare, but I discovered that it is possible (and I didn't take into account) for the query to result in a dataset with no data, i.e. a list of people with null values. I have to present those values as averages and standard deviations. It came as a nasty surprise when I found out that the DataTable.Compute() method doesn't handle a column of all nulls very gracefully. So, I coded up a solution, thanks to some Googling, which I believe lends itself to functional programming, something I just got introduced to by [Chris Eargle](http://www.kodefuguru.com/) at the last [Orlando DotNet UG](http://www.onetug.org/Home.aspx) meeting. Here's my initial stab and maybe I'll get some feedback on how to improve.  

  

```
using System;
using System.Collections.Generic;

namespace MathLib
{
    /// 
    /// Container class for all statistics values which may be set by the Statistics class
    /// 
    public class StatTypes
    {
        public double? Average { get; set; }
        public double? StdDev { get; set; }
    }

    public static class Statistics
    {
        /// 
        /// Gets the average and standard deviation of a set in one pass
        /// 
        /// The set to be operated on        /// A StatTypes object with the Average and StdDev values 
        ///    for the set operated on
        public static StatTypes GetAvgAndStdDev(this IEnumerable<double> values)
        {
            StatTypes avgAndStdDev = new StatTypes();

            // ref: http://warrenseen.com/blog/2006/03/13/how-to-calculate-standard-deviation/
            double mean = 0.0;
            double sum = 0.0;
            double stdDev = 0.0;
            int count = 0;
            foreach (double val in values)
            {
                count++;
                double delta = val - mean;
                mean += delta / count;
                sum += delta * (val - mean);
            }
            if (1 < count)
            {
                stdDev = Math.Sqrt(sum / (count - 1));
            }
            if (count > 0)
            {
                avgAndStdDev.Average = mean;
                avgAndStdDev.StdDev = stdDev;
            }

            return avgAndStdDev;
        }

        /// 
        /// Gets the average and standard deviation of a set in one pass
        /// 
        /// The set to be operated on        /// A StatTypes object with the Average and StdDev values 
        ///    for the set operated on
        public static StatTypes GetAvgAndStdDev(this IEnumerable<int> values)
        {
            StatTypes avgAndStdDev = new StatTypes();

            // ref: http://warrenseen.com/blog/2006/03/13/how-to-calculate-standard-deviation/
            double mean = 0.0;
            double sum = 0.0;
            double stdDev = 0.0;
            int count = 0;
            foreach (double val in values)
            {
                count++;
                double delta = val - mean;
                mean += delta / count;
                sum += delta * (val - mean);
            }
            if (1 < count)
            {
                stdDev = Math.Sqrt(sum / (count - 1));
            }
            if (count > 0)
            {
                avgAndStdDev.Average = mean;
                avgAndStdDev.StdDev = stdDev;
            }

            return avgAndStdDev;
        }
    }
}

```

  

Usage is made simple this way:  

  

```
string metric = "DaysWorked";
StatTypes avgAndStdDev = (from row in table.AsEnumerable()
                          where row.Field<double?>(metric) != null
                          select row.Field<double>(metric)).GetAvgAndStdDev();
calculatedTarget["Mean"] = (object)avgAndStdDev.Average ?? DBNull.Value;
calculatedTarget["StdDev"] = avgAndStdDev.StdDev;

```

  

As an aside, I had no idea how to name the class or namespace for any usefulness. However, I learned that I never use the class name, anyway, so perhaps it doesn't matter. *shrug*
