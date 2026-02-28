---
title: "Common Error, Simple Solution"
date: 2011-02-23
slug: "common-error-simple-solution"
tags: ["debugging", ".NET"]
description: "Have you ever seen this error?"
---

Have you ever seen this error?  

```
Message: Sys.ArgumentNullException: Value cannot be null.  
Parameter name: panelsCreated[2]
```

I struggled for quite some time trying to figure out what to do about it. If you do a Google search, you'll come across lots of people struggling with this error and the responses pointing at the AJAX Toolkit. However, in my case, I wasn't using the AJAX Toolkit directly. Rather, I was using Telerik's ASP.NET/AJAX controls.   

  

What I was doing seemed simple enough. I needed certain RadPanels to show up on the page while others are hidden (<- a="" ajax="" and="" as="" be="" behind="" big="" browser.="" by="" c="" cannot="" class="brush: cpp; toolbar: false" code-behind="" code="" control="" controls="" css="" d="" didn="" display="" displayed.="" do="" don="" error="" false.="" find="" foreshadowing="" frankly="" hidden="" hiding="" i="" if="" in="" include="" instead="" interface="" is="" issue.="" it="" key="" like="" look="" may="" modify="" no-no.="" not="" of="" or="" out="" pops="" pre="" property="" radpanel="" render="" root="" s="" same="" setting="" should="" simple="" so="" solution="" something="" t="" tag="" telerik="" that="" the="" their="" then="" this:="" this="" to="" toolkit.="" true="" turns="" up="" use="" visible="" want="" wants="" was="" when="" whether="" with="" you="">radDock.Style.Remove("display"); // Visible = true  

radDock.Style.Add("display", "none"); // Visible = false
