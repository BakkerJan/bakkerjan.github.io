---
title: "Evilginx Mastery Course | What I learned"
date: 2024-11-28
categories: 
  - "security"
image: "/assests/images/ms-teams_ujYBLm9ztT.png"
---

A couple of years back, I was really struggling to get Evilginx up and running. The main reason was that I had forgotten how to do it by the time I needed an instance for a demo, so it took me hours to spin it up.

Then, I decided to take notes, and I made [this post](https://janbakker.tech/how-to-set-up-evilginx-to-phish-office-365-credentials/). To this day, this is my go-to manual for getting Evilginx up and running from scratch. It contains all the steps, such as buying a domain, creating DNS records, buying a VM, and installing Evilginx.

It has served me well over the past few years, but then Kuba (creator of Evilginx) created the [Evilginx Mastery course](https://academy.breakdev.org/evilginx-mastery/12wvr?coupon=BLACKFRIDAY). This was a godsend. I remember binging the entire course as soon as it came out.

## Lifetime access ⌚

The best part of the course is that you will have access till the end of time. That counts for the entire course, labs, and access to the Discord channel, where you can chat with others and ask questions. That's real value for money if you ask me.

## The basics

The course will teach you all the basics about phishing, tokens, MFA, and web security. This will set the scene for the rest of the course. Step-by-step, you will learn how phishing works and how tokens are stolen. The course will also teach you how to set up your lab environment with browser profiles, extensions, and tricks about cookies.

## Building phishlets ✅

Evilginx is all about creating (and maintaining) phishlets. The course has very detailed information about the structure of phishlets, which resulted in building a [phishlet for Microsoft 365](https://github.com/BakkerJan/evilginx3/blob/main/microsoft365.yaml) that still works today. Before the course, I would copy and paste from existing sources and cross my fingers. If it did not work, I had no clue what was causing the issue. The course taught me how phishlets really work.

## Advanced phishing 🐟

Building a phishlet for Microsoft 365 is pretty straightforward once you understand the theory, but try building a phishlet for Google, for example, and you'll find yourself doing all kinds of ninja stuff like replacing content, forcing POST parameters, and injecting JavaScript.

The course has a complete section where you will go really deep on advanced features from Evilginx that you did not know existed like landing page redirectors and mass lure targeting.

![](/assets/images/image-8.png)

## Deployment 🧑‍💻

Evilginx can run both locally and on a remote server, and the course will explain all the parts of a successful deployment, including setting up the domain and the VPS/VM and ensuring that Evilgnx will keep running (persistence). This was the part I struggled with the most, as it involved many moving parts like installing and tweaking Linux distros, so it helped me to bump up my Linux knowledge as well.

## New content added

After I initially purchased the course, Kuba added several new items without charging extra money. This section will go deep into Microsoft 365 and Okta phishlets, which really helped me to polish my skills.

![](/assets/images/image-9.png)

## Building a better world without passwords

Overall, this course helped me to get a better understanding of how phishing works and why it is important that we show this to the world; passwords and legacy MFA are not good enough, and we need to get rid of them ASAP.

When you have the skills to think and act like an attacker, it will really improve your defense of token theft and thrive in passwordless and phishing-resistant authentication like passkeys. Every time I have done a demo using Evilginx, folks are stunned by how easy it is to phish for Microsoft 365 credentials. Showing what the bad guys (and girls) can do is a very important step in convincing stakeholders to get to the next level of identity security.  

[Buy now!](https://academy.breakdev.org/evilginx-mastery/12wvr?coupon=BLACKFRIDAY)

[![](/assets/images/image-68-1024x800.png)](https://academy.breakdev.org/evilginx-mastery/12wvr?coupon=BLACKFRIDAY)

Stay safe!

P.S. If you're low on budget, please check [this course](https://www.simplerhacking.com/courses/phishlet-creation-masterclass?ref=186519) by Simpler hacking.
