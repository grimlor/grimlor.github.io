---
title: "Silly Trick of the Quarter"
date: 2011-02-23
slug: "silly-trick-of-the-quarter"
tags: ["tips", ".NET"]
description: "Ok, the title would have sounded better as Silly Trick of the Day, but let's be honest; I don't post that often. :-) That said, here's something someone..."
---

Ok, the title would have sounded better as Silly Trick of the Day, but let's be honest; I don't post that often. :-) That said, here's something someone might enjoy. I've been working a lot with AJAX recently to improve the performance of a dashboard. No matter how fast I make the queries run, though, there will be times where we have to wait. So, the inevitable progress indicator comes up...boring, annoying, time for a cup of coffee.  

  

I decided to reward those who grin and bear it with an iconic symbol of activity, Pacman being chased by ghosts until he goes offscreen and then returns the favor by chasing the suddenly blue ghosts back off where they came from. However, I didn't want it to do this every time. Here's the cool trick, code provided below.  

  

```
/// 
/// Extends the Base.Render() method to randomly choose which image to show 
/// for Loading panel
/// 
/// protected override void Render(HtmlTextWriter writer)
{
    Image loadingImage = (Image)UpdateProgress1.FindControl("imgLoading");
    if (loadingImage != null)
    {
        int pacmanFlag = new Random().Next(1, 10);
        loadingImage.ImageUrl = pacmanFlag == 1
                                ? "~/Images/indicator-pm.gif"
                                : "~/Images/indicator-big.gif";
    }
    base.Render(writer);
}
```

  

The key is to create a Render() method which overrides the base class' method. Your asp:Image in your UpdatePanel on the page, named imgLoading in my case, gets randomly set to the specified ImageUrl.  

  

![](/images/posts/indicator-pm.gif)[](/images/posts/indicator-pm.gif)*Note: As my project is neither publicly facing nor used in the pursuit of any profit, I don't believe I'm breaking any copyright laws. If you wish to use Pacman as I have, I'm willing to share my graphic but be aware of how the copyright laws may affect your application.*
