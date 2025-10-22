---
title: "Power Automate: not your daddy's RSS reader"
date: 2021-06-16
categories: 
  - "power-platform"
image: "/assests/images/pexels-rene-asmussen-25758-1.jpg"
---

Here's a quick tip for all you eager learners out there. If you don't want to miss any new articles from your favorite website, consider using Power Automate to keep up to date.

![Power Automate - Microsoft Tech Community](/assets/images/xuPlE1EJ_400x400.png)

For example, you can add new articles straight to your Microsoft To-Do list, or maybe you want to be notified by email or chat about it. Let's have a look how easy it is to set up.

To start off, create a new cloud flow in Power Automate. Give it a proper name, and select _"When a feed item is published"_ as your flow's trigger.

![](/assets/images/image-6.png)

Most (WordPress) websites have a default feed URL built-in. You can simply check if this one exists, by browsing the website and adding /feed after the URL, for example, https://janbakker.tech/feed

Most website provide an RSS link somewhere, like this:

![](/assets/images/image-10.png)

Enter the RSS feed, and click **\+ New step**

![](/assets/images/image-7.png)

Next, you can do whatever you want, but to give you some ideas:

- Send an email
- Send a chat
- Add to a SharePoint list
- Add to an Excel file
- Tweet about it
- Post it on LinkedIn
- Add it to your To-Do list

In this example, I will add it to my Microsoft To-Do list. This way, your list will fill up with all kinds of interesting stuff over time, and you can simply check off the ones you read. Add the feed title and the primary feed link, and you're done!

![](/assets/images/image-8.png)

To spice it up, you can also send out a chat, to get notified that a new item is added.

![](/assets/images/image-9.png)

Now wait, for the first article to be realease, and check the result:

![](/assets/images/image-12.png)

That's it folks! Easy peasy. Hope you like it!
