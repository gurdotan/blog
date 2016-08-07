---
title: Detecting Corporate Email Signups with Zapier
description: Forget all of those Gmail, Hotmail and Yahoo signups. If you're after business prospects, this Zapier automation will help you identify your most qualified leads in no time.
author: Gur Dotan
date: 2016-08-07 22:12:57
tags:
share_cover: /corporate-emails-zapier/corporate-emails-zapier-og-banner.jpg
---

I'm about to show you how to monitor your SaaS product's signups with [Zapier](https://zapier.com/) and quickly detect the most valuable leads based on their email addresses.

## The Problem
In my previous company SOOMLA, one of the marketing challenges we faced was qualifying leads quickly.  Our mobile analytics product was then attracting dozens of signups everyday from a wide variety of customer personas.  While high signup numbers are something no company would turn down, it also created too much "longtail signup noise". Our product, being completely free, attracted mostly indie game developers and on rare occasion bigger studios.

We recognized that the best indication of highly qualified leads was a corporate email address. Signups originating from Gmail, Hotmail, Yahoo and the likes were usually passersby just checking out the goods and never coming back. But new users who signed up with a unique domain in their email address were from a real company with real traction.  These corporate signups were our VIP leads - we had to make sure these valuable prospects would be nurtured and not left behind.

## Zapier to the Rescue
To solve this quickly we used Zapier - we devised an automation sequence that rules out all generic email addresses.  The idea is to:

1. Set up a trigger whenever a new user signs up.
2. Pass it through a Zapier filter, and
3. Output only the corporate email signups to Slack, email, or wherever you want to get notified.

Steps 1 and 3 are rather trivial.  For the trigger you can use your CRM, a Google spreadsheet or even your MySQL / MongoDB database.  Whenever a new user entry gets logged in one of those systems, trigger an event with the user's email and details. For the output action it's most convenient nowadays to send a Slack message with the new lead's details, but you can use any other messaging service.

## Detecting Corporate Emails

Step two is the longest to set up.  We can't possibly detect all corporate emails in the world, but what we can do is filter out all those that are clearly not. You'll need to create a Zapier filter step that continues the sequence only if the email address doesn't match a bunch of unwanted patterns. For this you'll need to create lots of "AND" rules, each one connecting the `Email` field from step one with a "(Text) Does not contain" and the generic email domain.

>In plain English we're telling Zapier: "Monitor all incoming emails. Only if an email doesn't contain `gmail`, `hotmail`, `yahoo` etc. approve it and notify me!".

{% asset_img corporate-signups-01.png "Zapier Generic Emails Filter" %}

Setting up this filter is a lot of grunt work. Unfortunately Zapier doesn't let us share our Zap's configuration to save you the time, so I'll just share all the domains and patterns I've added to this monster filter:

- hotmail
- yahoo
- gmail
- googlemail
- live.
- outlook
- msn
- me.com
- icloud
- mailinator
- naver.com
- 163.com
- gmx
- hanmail
- aol.com
- mail.ru
- yandex.ru
- ya.ru
- bk.ru
- rambler.ru
- inbox.ru
- ukr.net
- ymail.com
- o2.pl
- wp.pl
- free.fr
- trbvm
- t-online
- comcast
- trixept
- lv2vr
- abv.bg
- nate.com
- test.com
- qq.com
- web.de

Note that some patterns intentionally don't include the `.com` suffix in order to capture lots of top level domains.  For example the pattern `yahoo` can filer out emails with `yahoo.com`, `yahoo.co.uk`, `yahoo.de` etc. As we received more signups I kept updating the filter with more generic domains that I learned about.

**Tip**: You can also your own company's domain to the filter so you won't see all those fake users your developer created while debugging the signup process. Yeah, we've all committed that sin.

## Conclusion

Using Zapier, we've been able to relax the way we monitor inbound leads. In the early days, we used to just pounce on each new signup with a hand written email attempting to collect product feedback.  In many cases we'd never get an answer and this was highly correlated with the generic email signups. With the new automation in place, we could get notified only when highly qualified leads would come in. Better yet, we could connect them to further steps in the Zapier automation sequence such as drip email campaigns.

**Note 1**: I'm a huge fan of [Zapier](https://zapier.com/)! If you call yourself a marketer and haven't yet heard of Zapier, please crawl out from under the rock you've been hiding.  It is in my opinion the single most important marketing automation tool out there, and an essential part of your martech stack.


**Note 2**: [Intercom](https://www.intercom.io/) has a similar feature baked into their daily emails. I cannot attest to fully understand their signup scoring system but it generally appears their daily email ranks corporate signups higher than others.

{% asset_img intercom-daily-email.jpg "Intercom Daily Email" %}

