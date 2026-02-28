---
title: "Windows Service or Console App? Why not both!"
date: 2013-04-08
tags: ["debugging", ".NET", "windows service"]
slug: "windows-service-or-console-app-why-not-both"
description: "In my organization, I have some folks who love Windows Services and others who despise them, preferring a regular Console App that they can schedule to run..."
---

In my organization, I have some folks who love Windows Services and others who despise them, preferring a regular Console App that they can schedule to run using the Windows Task Scheduler. (It's no surprise that the opposite dislike exists.) Further, everyone knows that debugging a Windows Service can be painful. So, simplify your life and make everyone happy (a rarity indeed)! Here's how to do it:  

  

**YourService.designer.cs:**  

```
using System;
using System.ServiceProcess;
using System.Timers;

namespace YourNamespace
{
    public partial class YourService : ServiceBase
    {
        #region Constructors/Destructors

        public YourService()
        {
            InitializeComponent();

            // TODO: Add code here to initialize your service
        }

        #endregion

        #region Public Methods

        // Added to allow for the Console app to start.
        public void StartConsole(string[] args)
        {
            OnStart(args);
        }

        // Added to cleanly stop the Console app.
        public void StopConsole()
        {
            OnStop();
        }

        #endregion

        #region Overrides

        protected override void OnStart(string[] args)
        {
            // TODO: Add code here to start your service.
        }

        protected override void OnStop()
        {
            // TODO: Add code here to perform any tear-down
            // necessary to stop your service.
        }

        #endregion
    }
}

```

  

Proper attribution for the [meat of the code above](http://einaregilsson.com/run-windows-service-as-a-console-program/) needs to be given to [Eienar Egilsson](http://einaregilsson.com/). Go check out the many contributions he's made to the art of development.  

  

**Program.cs:**  

```
using System;
using System.ServiceProcess;

namespace YourNamespace
{
    static class Program
    {
        /// <summary>
        /// The main entry point for the application.
        /// </summary>
        public static void Main(string[] args)
        {
            YourService service;

            if (Environment.UserInteractive)
            {
                Console.WriteLine("Press Escape to stop program");
                service = new YourService();
                service.StartConsole(args);
                while (true)
                {
                    var cki = Console.ReadKey();
                    if (cki.Key == ConsoleKey.Escape) break;
                }
                service.StopConsole();
            }
            else
            {
                service = new YourService();
                var servicesToRun = new ServiceBase[] { service };
                ServiceBase.Run(servicesToRun);
            }
        }
    }
}

```
