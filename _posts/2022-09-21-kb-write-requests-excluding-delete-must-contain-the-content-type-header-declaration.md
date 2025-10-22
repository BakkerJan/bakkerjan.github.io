---
title: "KB - Write requests (excluding DELETE) must contain the Content-Type header declaration."
date: 2022-09-21
categories: 
  - "knowledgebase"
image: "/assests/images/pexels-mikhail-nilov-7534781.jpg"
---

This is a knowledgebase item. I hope it helps you out someday.

## The issue

When using the HTTP action in Power Automate or Logic Apps in combination with Graph API, you get the following error:

_Write requests (excluding DELETE) must contain the Content-Type header declaration._

![](/assets/images/image-9.png)

Despite having a header included, you still got prompted with this error message.

## Cause

In my case, this happened when the API required a body that I did not provide. I used it to create a Temporary Access Pass, and if you don't add a body, the default settings will apply.

## Solution

Use the [documentation](https://learn.microsoft.com/en-us/graph/overview?view=graph-rest-beta) to see what body is required. Otherwise, simply adding {} to the body will fix issue.

Stay safe!
