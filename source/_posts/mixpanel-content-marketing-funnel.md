---
title: Measuring Your Content Marketing Funnel With Mixpanel
description: Learn how to track call-to-action clicks using Mixpanel and Google Tag Manager. Use the collected data to build a funnel that shows how well your content marketing efforts convert users.
author: Gur Dotan
date: 2016-06-23 16:37:12
share_cover: /mixpanel-content-marketing-funnel/og-banner.jpg

---

One of the things I remember that baffled me for a long time in content marketing was the fact that it was hard to understand if the content we created was actually converting users and pushing them lower down the funnel.

If a user read a blog post, would they complete a call to action in the content? Would they later sign up and engage with our platform? And if they did, which piece of content brought them in? Giving a local answer for a specific blog post was easy, but analyzing all posts in one funnel accurately was a pain.

In this tutorial I'll show you how to implement content conversion measurement using [Mixpanel](https://mixpanel.com/), [Google Tag Manager](https://www.google.com/analytics/tag-manager/), and a toss of HTML, step by step.

#### Pre-requisites:
* A Mixpanel account
* A Google Tag Manager account
* A basic understanding of HTML

## Defining The Funnel

Let's start with defining our funnel's goal in one sentence: we want to measure which content performs best by attributing acquired users (signups) to the content they came from.  For the sake of this article we'll assume that "content" refers to blog posts on our blog.

Let's break this down into action items:

1. Place call-to-actions (CTAs) in each blog post.  Make sure the CTAs have meaningful context. Avoid disrupting the user's reading experience with blantanty positioned banners.
2. Capture click events on CTAs and report them to your analytics service.
3. Report at least one more event to your analytics service from any user engagement which is past the signup step.  For example, the first page load inside your product or dashboard.


## Building the Funnel

We'll start from step #2 - we'll capture click events on CTAs with Google Tag Manager, and we'll report them to Mixpanel. 

### Set up Google Tag Manager

1. Head over to https://tagmanager.google.com/ and sign in with your Gmail account (or Google for business email).
2. Create a new account
3. Setup your account
{% asset_img gtm1.png %}
4. Setup up a container URL.  This is effectively the domain where you'll be adding the Google Tag Manager script tag.  Select "Web". 
{% asset_img gtm2.png %}
5. Copy the GTM script tag and place it in your website's master template. Alternatively, if you have static pages that aren't dynamically served or built, just paste this code in each one of them.
{% asset_img gtm3.png %}
6. Make sure your website pages are live with the script tag and try testing GTM by adding some tag and publishing it successfully.  Refer to the [GTM documentation](https://support.google.com/tagmanager/?hl=en#topic=3441530). If your site is hosted on WordPress, refer to [this article](http://www.wpbeginner.com/beginners-guide/how-to-install-and-setup-google-tag-manager-in-wordpress/) on how to add the script tag.

### Add GTM Tags to report events

1. From the left menu navigate to the tags screen and add a new tag.
2. Choose "Custom HTML Tag" and hit "Continue".
{% asset_img gtm4.png %}
3. Next, we need to configure the Mixpanel script tag. Head over to the [Mixpanel docs](https://mixpanel.com/help/reference/tracking-an-event) and copy their initialization code. Paste it into the big text box in this step.  Don't forget to replace `"YOUR TOKEN"` with your token inside the Mixpanel tag.  Hit "Continue".
4. Select "All Pages" so that the Mixpanel script tag will run on all of your website's pages.  By now your tag setup should look like this:
{% asset_img gtm5.png %}
Finally hit "Create Tag" and give it a name, say "Mixpanel".
5. Now for the actual call-to-action event reporting. Repeat steps 1-4, only this time enter the following custom HTML into the tag configuration:
```js
<script>
  var $ = window.jQuery;
  if (window.mixpanel) {
    mixpanel.track('page_loaded', {source: 'blog'})
  }
  
  $(function() {
    $('.cta').click(function(e) {
      e.preventDefault();
      e.stopImmediatePropagation();
      
      if (window.mixpanel) {

        var $el     = $(e.currentTarget),
            action  = $el.text().trim(),
            page    = document.title.trim(),
            method  = $el.data('method');

        mixpanel.track('Call To Action', {
          source : 'blog',
          Post   : page,
          Action : action,
          Method : method
        }, function() {
          window.location = $el.attr('href');
        });
      }
    });
  });
</script>
```
  **Quick explanation:** the javascript snippet does two things:
  1. Sends a `page_loaded` event to Mixpanel from the current blog post.
  2. Captures clicks from all elements on the page with the `cta` class, be it links buttons or any other element. Once a call-to-action is clicked, we send an event called "Call to Action" to Mixpanel. The event tracking code assumes that the call-to-action HTML element is structured as such:
  `<a class="cta" href="https://some.domain/signup" data-method="button">Signup Now</a>`

  Also observe that we're sending some attributes with the "Call to Action" event:
  * `source` - which property the click came from (e.g. "blog", "website", "whatever")
  * `Post` - the blog post title (as seen in the browser title bar)
  * `Action` - the call-to-action text inside the link / button / other element.
  * `Method` - defines if this is a button, a link, or any other type you want to report.

6. Create the tag and head back to the "Overview" screen.
7. Publish your container by hitting the "Publish" button.

**Note**: Mixpanel doesn't support deleting data easily. If you'd like to test things out without littering your existing project's analytics, create a new project for experimentation purposes and use that project's API key instead. 
 
### Update Call-To-Actions In Content

We've set up the javascript logic to send events when CTAs are clicked. Now we need to adapt the links to include the necessary class name and attributes in order for them to report events properly to Mixpanel. Head over to some blog posts or articles in your website and change their CTAs (links, buttons, banners, etc.) to accommodate the `cta` class and the necessary meta-data that will be reported to Mixpanel.  Here are some examples:

* `<a class="cta" data-method="link" href="/signup">Sign Up Now</a>`
* `<a class="btn cta" data-method="button" href="/getstarted">Get Started</a>`
* `<div class="banner-wide cta" data-method="banner" href="/promo">Free Trial</a>`


### Create A Mixpanel Funnel

Open your Mixpanel account and head to the "Funnels" page.  Make sure that the selected project (in the top left menu) is the same one you took the API key from. Then:

1. Click the plus sign to create a new funnel
2. Fill in three steps. The first step is the page load originating from the source `blog`. The 2nd step is the CTA from the same source. The 3rd step is any applicative event you report to Mixpanel associated with the conversion.  This could be a sign up event for example, or an action your users perform one time after they sign up such as submit set up data.  In this example I'm measuring an event called "Game Created" which is an event reported after a user has signed up to SOOMLA and has created their first game in the dashboard.
  {% asset_img mixpanel1.png "Mixpanel Funnel Setup" %}

**Note:** this article doesn't cover how to report the post-conversion event(i.e. after signup). In a nutshell, it's as easy as: `mixpanel.track('Signup', {source: 'dashboard'})`.  Consult the [Mixpanel docs](https://mixpanel.com/help/) for more info.

## Analyzing The Funnel

Here's what the funnel looks like for January 2016:
{% asset_img mixpanel2.png "Mixpanel Funnel" %}

To break down the results and understand which content is performing best, we can choose "Current URL" as the grouping at the bottom of the page.
{% asset_img mixpanel3.png "Mixpanel Funnel URL Breakdown" %}

This gives a much better picture. It is now easy to assert that the most conversions originate from the highest-traffic page at http://blog.soom.la/2015/01/top-10-unity-games-ever-made.html.  On the other hand, the best conversion ratio lies in the page http://blog.soom.la/2015/12/soomlas-free-unity-plugins-go.html.  These are different blog posts in essence - the former being a SEO optimized page for attracting gamers and indies, while the latter is a targeted message to SOOMLA's existing user base.

Another interesting analysis is to see which type of CTA performs best. If we breakdown the data by "Method", we can immediately conclude that buttons work much better than links:
{% asset_img mixpanel4.png "Mixpanel Funnel Method Breakdown" %}



## Discussion

The results of this funnel are controversial. With an overall funnel completion ratio of 0.05%, one would be tempted to think that the blogging effort isn't yielding any customers. With blog posts leading only 1.45% of visitors to click on CTAs, how can you expect to build your product's user base with content marketing?

Industry wide statistics about such blog-post-visit-to-conversion ratios are hard to come across. One [benchmark by Moz](https://moz.com/blog/ecommerce-kpi-benchmark-study) claims that online retailers, while serving a completely different audience than the SOOMLA blog, see 1.4% conversion. Using that statistic as vague reference tells us that blog post to customer conversion is actually in the norm. 

However, the big picture is composed of myriad details not captured by this funnel. How did visitors reach the content? What percentage of visitors was organic i.e. search driven? What is the average session length and bounce rate? Were there other marketing efforts that contributed to the incoming traffic?  Were some of these visitors already signed up for the mailing list or the service? Answering all these questions requires more sophisticated measuring.

The purpose of creating this funnel is to gain a simple piece of visibility into trends of your customers' lifecycle. A single snapshot is nice to have, but a history of weekly snapshots is much more indicative of your content's performance over time. Once in place, you can create a weekly routine of tracking this funnel in a spreadsheet to identify trends and effects of CTA placements inside the content.

## Notes

* It is sufficient to add one event to the funnel that is reported after signup or conversion.
* Google Tag Manager can also be set up differently with variables and custom triggers, in a way that doesn't require any Javascript code. As a hacker it's easier for me to code in exactly what I want.
* The examples in the article use several formats for event names. This is bad practice and due to legacy naming we had in Mixpanel. Opt for short, plain English event names without any `camelCase` and `snake_case` complexities.
* Theoretically the funnel also captures a user who has read two articles with CTAs, but only clicked the CTA in the 2nd article. If you want to examine a specific blog you'll need to modify the 1st step in the funnel to a `page_loaded` event where the `Post` property equals the title of the blog post you'd like to measure.
* This funnel can also be built rather easily with [Amplitude](https://amplitude.com/) instead of Mixpanel.
