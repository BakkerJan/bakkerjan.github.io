---
title: "A Thread on Frosty Fiascos: Delving into the Microsoft Midnight Blizzard Hack"
date: 2024-02-03
categories: 
  - "entra"
  - "security"
coverImage: "52ebccc7-a9e6-4546-85c4-5cd300355a04.jpeg"
---

This post is all about the hack on Microsoft by Midnight Blizzard (NOBELIUM, Cozy Bear, APT29)  
A lot has been said already, and this event has many angles. This post is focused on one part:

**What can Entra/Microsoft 365 admins do to prevent such an attack?**

I don't have all the answers (certainly not!) but I feel the urge to bundle all the resources so that the community can find guidance during this snowstorm. I did not find any time to put something on paper myself, but this hack has kept me busy for the past few days. During that period, a lot of good resources already came out, so that's what I'd like to share.

## TL;DR

1. The attack was not the result of a vulnerability in Microsoft products or services. The attacker gained access through unprotected accounts, overprivileged apps, and a lack of governance and alerting.

3. The motive was most likely to gain insights into the information Microsoft had about the attacker. At one point, it seemed that the attacker had unlimited control over the entire production tenant, but they "just" exfiltrated email and documents from targetted accounts.

5. Admins should review all service principals that have the highest privilege Entra ID roles and MS Graph app roles in the first place. Then follow up on the recommendations in the various blogposts.

## Microsoft publications

[Microsoft Actions Following Attack by Nation State Actor Midnight Blizzard | MSRC Blog | Microsoft Security Response Center](https://msrc.microsoft.com/blog/2024/01/microsoft-actions-following-attack-by-nation-state-actor-midnight-blizzard/)  
[Midnight Blizzard: Guidance for responders on nation-state attack | Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/2024/01/25/midnight-blizzard-guidance-for-responders-on-nation-state-attack/)  
[Midnight Blizzard (NOBELIUM) News and Insights | Microsoft Security Blog](https://www.microsoft.com/en-us/security/blog/tag/midnight-blizzard-nobelium/?sort-by=newest-oldest&date=any)  
**(Update 03/08/2024)** [Update on Microsoft Actions Following Attack by Nation State Actor Midnight Blizzard | MSRC Blog | Microsoft Security Response Center](https://msrc.microsoft.com/blog/2024/03/update-on-microsoft-actions-following-attack-by-nation-state-actor-midnight-blizzard/)

This podcast is created by two Microsoft employees, [Andy Jaw](https://twitter.com/ajawzero) and [Adam Brewer](https://twitter.com/ajbrewer)

https://www.youtube.com/watch?v=DpMp0\_9skWE

## Community resources

A thread on X: [Clément Notin op X: 'What I think happened in the Midnight Blizzard breach of Microsoft: how could they pivot from the test tenant to the production tenant using a OAuth application? 🤔⤵️ https://t.co/GNhJDZeDAZ' / X (twitter.com)](https://twitter.com/cnotin/status/1751028257405665773?s=46&t=AxQkhD9sR9508NnvCAhibw)

[Microsoft Breach — What Happened? What Should Azure Admins Do? | by Andy Robbins | Feb, 2024 | Posts By SpecterOps Team Members](https://posts.specterops.io/microsoft-breach-what-happened-what-should-azure-admins-do-da2b7e674ebc)

https://www.youtube.com/watch?v=e1meQ3\_yRIo

Here's a good visual from [Amitai Cohen 🎗️ (@AmitaiCo) / X (twitter.com)](https://twitter.com/AmitaiCo)

![](/assets/images/image-1024x463.png)

Blogpost from [Jeffrey Appel | Microsoft MVP (@JeffreyAppel7) / X (twitter.com)](https://twitter.com/JeffreyAppel7)

[Pivot via OAuth applications across tenants and how to protect/detect with Microsoft technology? (Midnight blizzard) (jeffreyappel.nl)](https://jeffreyappel.nl/pivot-via-oauth-applications-across-tenants-and-what-to-for-protection-with-microsoft-technology-midnight-blizzard/)

If you are using BloodHound, here's a good read: [Microsoft Breach — How Can I See This In BloodHound? | by Stephen Hinck | Feb, 2024 | Posts By SpecterOps Team Members](https://posts.specterops.io/microsoft-breach-how-can-i-see-this-in-bloodhound-33c92dca4c65)

[Microsoft vs Midnight Blizzard (FULL EPISODE) | SS Podcast | EP34](https://www.youtube.com/watch?v=97RrHgi_SPA) and [The Security Swarm Podcast - EP24: The Danger of Malicious OAuth Apps in M365](https://www.youtube.com/watch?v=4hwMcMi85YU)

Merill added a new cmdlet (Export-MsIdAppConsentGrantReport) to the MSIdentityTools module at [https://aka.ms/MSID](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbi1yY3Jvd2ZwdF9SaHFnQ0ROTXJtdHpKYzE1QXxBQ3Jtc0ttbU9FaW5QVVVVYWZXVjhfUllGNmlfSXdRUkRfRGlLQ2NQdXJEVmNLa2I5NERUc0dfcDFJdm9mcTdwRmQ4Ui14RHlHRlVSYnZMVUgzTEQ0VGZNWHFrYTc0ZWxqYy1FZkhkRThOVFk2c1VpS0Zpd2VhUQ&q=https%3A%2F%2Faka.ms%2FMSID&v=vO0m5yE3dZA) which scans your Microsoft 365 tenant, reviews the permissions of apps, users and the permissions you've granted to them. Learn more in the video:

https://www.youtube.com/watch?v=vO0m5yE3dZA

## What's next?

I just wanted to put this post out there, for everybody to learn the details of this attack. I will update the post as more details come out. For now, stay safe!
