---
title: "Error 500 referencing VSEnterpriseHelper when running unit test of ASP.NET in VS2010"
date: 2010-07-13
tags: ["unit testing ASP.NET 3.5 site Visual Studio 2010 errors"]
slug: "error-500-referencing-vsenterprisehelper-when-running-unit-test-of-aspnet-in-vs2"
description: "As noted previously, I want to move to test-driven development. My first step towards that end is to add unit tests to my team's existing projects. So,..."
---

As noted previously, I want to move to test-driven development. My first step towards that end is to add unit tests to my team's existing projects. So, while working to retrofit a web dashboard project I co-authored to use my new DataFactories class, I added unit testing to it. Upon every attempt to run the first test, however, I was receiving the following error:  

  

```
The test adapter 'WebHostAdapter' threw an exception while running test 'MyMethodTest'. The web site could not be configured correctly; getting ASP.NET process information failed. Requesting 'http://localhost:2212/VSEnterpriseHelper.axd' returned an error: The remote server returned an error: (500) Internal Server Error.
The remote server returned an error: (500) Internal Server Error.

```

  

It turns out that this can be caused by numerous things, including not having access to your data source for the project. Having confirmed that, though, you may still have the issue. I chased my tail on this for a while until I found a Microsoft Connect (bug submission site) post. Here's the jist: VS2010 only supports testing in DotNet 4.0. However, if you're running a 3.5 web site then testing will give you this error. There is an easy workaround, as follows:  

  

1. Open Web.Config  
2. Under the <runtime> element, *before* the <assemblyBinding appliesTo="v2.0.50727" xmlns="urn:schemas-microsoft-com:asm.v1"> element, add the following:  

  

```
<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1"/>

```

  

3. The <runtime> element should look something like this:  

  

```
<runtime>
  <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1"/>
  <assemblyBinding appliesTo="v2.0.50727" xmlns="urn:schemas-microsoft-com:asm.v1">
    <dependentAssembly>
      <assemblyIdentity name="System.Web.Extensions" publicKeyToken="31bf3856ad364e35"/>
      <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="3.5.0.0"/>
    </dependentAssembly>
    <dependentAssembly>
      <assemblyIdentity name="System.Web.Extensions.Design" publicKeyToken="31bf3856ad364e35"/>
      <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="3.5.0.0"/>
    </dependentAssembly>
  </assemblyBinding>
</runtime>
```

  

4. Save and close your Web.Config. If you don't, you'll get notifications that it's being changed externally, which is actually the debugger converting that section into something it can use.
