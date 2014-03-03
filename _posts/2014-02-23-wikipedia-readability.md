---
layout: post
categories: [example]
tags: [table]
class: "services"
---

[![Preview](http://i.imgur.com/5aTnUwz.jpg?1)](http://i.imgur.com/5aTnUwz.jpg)

##Removing Margins and Sidebars

There's a lot of not too useful content lingering all over. Here's the tags I remove along with why for each of them.

1. I'm not too keen on editing articles, because in the past I've tried to make edits (with valid sources) and still had them undid by the subject author rather consistently. So let's remove #mw-head!
2. There's a huge unnecessary top margin space. Because of the url bar, tabs, and bookmarks, the content is already pushed down to eye level, so there goes #mw-page-base.
3. Unless you're going to random pages or changing languages a lot, you'll probably never use the sidebar at all. It just conflicts with the main usage of the site, say farewell to #mw-panel.

###Code:
    #mw-head, #mw-page-base, #mw-panel {
        display: none;
    }

##Sizing All Around

Since I'm more used to the 960px grid format, I decided to stick with that. Then it's only a matter of calculating how big you want your font size and content width alongside your line height. I also think that the font-size is relatively good for what it is. So all I have to do now is plop in the numbers to a calculator and see what line-height should be around. I found a neat [Golden Ratio Typography Calculator](http://www.pearsonified.com/typography/) that does the work for you.

For me it calculated 26px when I used 13px for the font size. I eyeballed it and decreased it to 24px to give it a nice feel. I also adjusted the paragraph margins so that it's easy again to tell them apart.

###Code:
    #content {
        margin: 0 auto;
        width: 960px;
    }

    p {
        line-height: 24px;
        margin: 10px 0;
    }

##Persistence

To keep these css changes to stay on all the time, I use the Stylebot extension for Chrome. I believe you can use Stylish for Firefox, but I haven't used it yet.