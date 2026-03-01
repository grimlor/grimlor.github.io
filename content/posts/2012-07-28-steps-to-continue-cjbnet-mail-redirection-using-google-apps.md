---
title: "Steps to continue CJB.NET mail redirection using Google Apps"
date: 2012-07-28
tags: ["catch-all mailbox", "CJB.net", "Google Apps"]
slug: "steps-to-continue-cjbnet-mail-redirection-using-google-apps"
description: "Foreword: CJB.NET discontinued service of catch-all mail forwarding at the end of July 2012 with a month's notice. If, like me, you found this service..."
aliases:
  - "/2012/07/steps-to-continue-cjbnet-mail.html"
---

Foreword: CJB.NET discontinued service of catch-all mail forwarding at the end of July 2012 with a month's notice. If, like me, you found this service amazingly useful over the years it was provided free of service, this came as quite a blow. I used it for all my correspondence with corporations where I'd hand out an email address of @jackpines.cjb.net. So, you can imagine how many companies have been emailing me over the years. It allowed me to turn them off at any time even if they didn't give me an option of unsubscribing. It also let me know when they sold my email address.

  

Now, I'd be happy to pay for such a service. It was very useful. However, I wouldn't pay much because it's one of those services that once it's set up there's no maintenance. Further, the setup is done one time no matter how many users use the service. I'd say an annual, nominal fee for maintaining the hardware and internet connection is appropriate. However, CJB.NET never charged but, further, they didn't even offer that option. The only choice when the notice of discontinuation came was to find another option.

  

One other option was to sign up with [Redirection.net](http://redirection.net/) but it seemed like they were in bed with CJB.NET so it felt like a really long con bait-and-switch to me. Besides, I found out about that only after I had started down another, difficult path. The path was difficult but the solution was brilliantly simple. I present that solution, below. The path I took was arduous only because I was blazing trails that weren't well marked so I hope that if you trip over this soon enough then you find it useful.

  

1. Register a domain. The cheapest option is one of the new .INFO domains. I highly recommend using a private registration so that your personal contact information doesn't show up in a WhoIs search. Total cost via [cheap-domainregistration.com](http://cheap-domainregistration.com/) was $4.12 for a year.
2. [Create a Google Apps account](https://www.google.com/a/cpanel/standard/new) for the domain registered in step 1. This will involve confirming the domain ownership. If your registrar isn't in the list then you must have access to add a TXT record on your DNS server. On [cheap-domainregistration.com](http://cheap-domainregistration.com/), the DNS Manager lets you do so easily. Google gives you something that looks like this:

  

> 

google-site-verification=aBc123CbA321aBc123CbA321aBc123CbA321aBc123CbA321

This may be obvious to many but it's one of the trial/error steps I took that I'll save you from; simply put an @ in the Host field while the string Google provides goes into the TXT Value field.

  

3. Now wait an hour.
4. No, I'm serious. Wait an hour. Not waiting for my DNS zone updates to propagate is what made me continue my trial and error far longer than necessary. It*may not take an hour* but it can take that long so **don't go changing things up thinking you got something wrong until an hour has passed**. Go play some Skyrim or something.
5. Login to Google Apps and step through the setup. It doesn't put you there immediately but that is where you want to go. The first thing it'll do after asking you some easy questions is help you change your MX records in your DNS configuration.
6. Google will tell you that it takes 48 hours for that to take effect. However, as Google says, you can continue through the Google Apps setup. The next step tells you how to access your mailbox via webmail. It mentions that it will show you how to simplify the URL to use your domain. It doesn't so for your edification, here's what you should do.
1. In Google Apps, go to the Settings screen (tab on top navbar).
2. Select Email under Services (left navigation menu).
3. Select Change URL.
4. Change the radio button to the second option and set the alias you want to use. By default, mail is entered for your domain. Continue to the next screen.
5. You're instructed to add a CNAME record for your domain equating mail to [ghs.googlehosted.com](http://ghs.googlehosted.com/).
6. Once done, confirm the fact. You'll be taken back to the dashboard.
7. Again, it may take an hour for your DNS change to propagate. However, for me it was much faster than that. Further, navigating to this new alias and logging in seemed to trigger something in Google Apps which made it see my updated MX records.
7. To continue with the setup, select Setup from the top navbar. I won't take you through the rest of the settings because they are not germane to this discussion.
8. I highly recommend [securing your mail server via SPF](http://support.google.com/a/bin/answer.py?hl=en&answer=178723) to help prevent spam.
9. The main point of this is to have continuity of your [CJB.NET](http://cjb.net/) catch-all mailbox so here's what you need to do to configure a catch-all in Google Apps.
1. Go to Settings (top navbar)
2. Select Email (left navigation menu)
3. Scroll down to the Catch-all address section
4. Select to forward the email to the address you setup when creating the Google Apps account. You could choose to have a new catch-all mailbox, if you wish, enhancing the feature you've enjoyed with [CJB.NET](http://cjb.net/).
10. If, like me, you don't want to have to check another mailbox then you can also forward your email from Google Apps and be exactly like [CJB.NET](http://cjb.net/)'s service. Here's how:
1. Login to your Google Apps mailbox. If you followed all the steps, then it should be as simple as navigating to mail. and signing in with the Google Apps credentials.
2. Go to Settings (gear at top-right and then Settings)
3. Select Settings and POP/IMAP
4. Click Add a forwarding address and enter your email that [CJB.NET](http://cjb.net/) was forwarding to
5. You will be sent an email with a link to confirm the address as valid to forward to
6. Sign into your domain email again (sign out first, if you didn't already)
7. Go to Settings, Add a forwarding address again
8. Choose the email address to forward to and decide what you want Google Apps to do with the mail in your domain's mailbox. 
9. Save changes

Up until now, this has just been an exercise in Google Apps. Here's where the magic happens that ties your new domain with Google Apps to your old[CJB.NET](http://cjb.net/) account.

  

11. I assume that you were pointing your [CJB.NET](http://cjb.net/) subdomain to someplace that you control. If not, then it's time to setup a blog or web page somewhere that you can control. I used Blogspot, another fine, free Google service, aka Blogger.
12. Login to your Google Apps account
13. Select Domain settings (top navbar)
14. You can choose to deactivate the test domain alias that Google Apps creates for you. I can't see using it but I didn't choose to delete it, just deactivate it.
15. Select to Add a domain or domain alias
16. Select the Add another domain option and enter your [CJB.NET](http://cjb.net/) subdomain
17. When you select to Continue and verify domain ownership, you'll be given a meta tag to add to your web page to which you must have your [CJB.NET](http://cjb.net/)subdomain redirecting
18. It bears repeating another way; follow the directions for adding that tag to the head of your page and make sure your [CJB.NET](http://cjb.net/) subdomain is pointing to that page. If you weren't forwarding your [CJB.NET](http://cjb.net/) subdomain to a web page previously, **it could take an hour for redirection to work**.
19. In Google Apps, proceed with the verification. 
20. Click on setup an MX record.

  

**Note:** This is the step that will *temporarily *break mail redirection with [CJB.NET](http://cjb.net/). This outage will be temporary but you shouldn't count on getting email during this time.

  

21. Login to your [CJB.NET](http://cjb.net/) domain and enter [ASPMX.L.GOOGLE.COM](http://aspmx.l.google.com/). into the Mail Server field, saving the changes when done.
22. Again, this change may take an hour to propagate.

  

That's it. Seems like a lot of steps but it was a lot more trial and error for me so this seems really easy with that in mind. I just worked through this with my wife's [CJB.NET](http://cjb.net/) subdomain so I know it works.
