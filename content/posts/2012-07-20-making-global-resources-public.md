---
title: "Making global resources public"
date: 2012-07-20
tags: ["resources", ".NET"]
slug: "making-global-resources-public"
description: "I am posting this here strictly as a reminder for myself but if anyone wants to comment about how bad a practice this is then please feel free. [This is..."
aliases:
  - "/2012/07/making-global-resources-public.html"
---

I am posting this here strictly as a reminder for myself but if anyone wants to comment about how bad a practice this is then please feel free. [This is probably a better solution.](http://odetocode.com/Blogs/scott/archive/2009/07/16/resource-files-and-asp-net-mvc-projects.aspx)  

  

If you've ever asked this question then either read on [or go get it from where I did](http://holyhoehle.wordpress.com/2010/02/20/making-global-resources-public/). I lifted this from [Holyhoehle's Blog](http://holyhoehle.wordpress.com/) and just wanted to be sure I could get the answer again in the future should his blog go offline.  

  

[Y]ou may want to use strings from a global resources .resx file (App_GlobalResources) as error messages or descriptions in your attributes. This won’t work because global resource files are by default marked as internal and so you can’t access them from your controller or your model. Also the little “Access Modifier” dropdown menu in the resource editor is grayed out, so you can’t change the access level without further ado.

However there’s a way to change the access rights without editing the designer file by hand.

You just have to open the file properties of the resource file and change the “Custom Tool” from “GlobalResourceProxyGenerator” to “PublicResXFileCodeGenerator”, which is the default Tool for local resource files. Next you have to change the “Build Action” to “Embedded Resource”. You may also want to assign a proper Custom Tool Namespace like “Resources” in order to access the file properly, but this isn’t necessary.

![](http://holyhoehle.files.wordpress.com/2010/02/globalresourceaccess.png)

Now rebuild the project and you should notice that the resource access is now public and you can access its contents from anywhere in your project.

E.g. like that:

```
[Required(ErrorMessageResourceType = typeof(Resources.Resource1), ErrorMessageResourceName = "Comment_Text")]
public string Myproperty { get; set; }

```
