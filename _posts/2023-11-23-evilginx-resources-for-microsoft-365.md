---
title: "Evilginx resources for Microsoft 365"
date: 2023-11-23
categories: 
  - "security"
coverImage: "9e391d8c-f87d-4387-997c-306acff856da.jpeg"
---

Okay, let's start with a disclaimer. This post is created for educational purposes and mainly focuses on red and blue teamers to protect their Microsoft 365 tenants. That being said, Evilginx can be used for all cloud services. **Do not** use Evilginx for nasty and illegal stuff. I won't help to fix your Facebook, Instagram, and TikTok phishlets. Feel free to reach out for security tips on Microsoft 365 / Entra ID.

This post will grow over time. If you have anything to add, please reach out to me so I can add it. The goal is to have a single page with the best public resources around Evilginx, focusing mainly on Microsoft 365.

## 1\. Know your stuff

First and foremost, it's crucial to understand the Evilginx framework deeply. When I started using this tool years ago, I copied and pasted work from others until it worked, without knowing what I did. When my phishlet stopped working (it does from time to time), I could not fix it myself.

So what changed? I learned from the master [Kuba Gretzky](https://twitter.com/mrgretzky) himself! Kuba recently launched the [Evilginx Mastery](https://academy.breakdev.org/evilginx-mastery/12wvr), and that really helped me understand the framework inside and out. I won't go into too much detail about the course, but these are the main things I learned:

- Getting Evilginx running on my local Windows machine so I can easily develop and test

- How to build my own phishlets from scratch

- How to maintain and improve my phishlets

- How to do JS injection, forcing POST parameters, and replacing context

- How to deploy Evilginx on a remote server

- How to set up phishing campaigns

I would really recommend checking out the course so you'll have a deep understanding of the framework. Warning: it can do epic stuff and is constantly improving.

[![](/assets/images/image-68-1024x800.png)](https://academy.breakdev.org/evilginx-mastery/12wvr)

[Evilginx Mastery course](https://academy.breakdev.org/evilginx-mastery/12wvr)

If you're on a tighter budget, I recommend [the courses](https://www.simplerhacking.com/?ref=186519) from Simpler Hacking.

![](/assets/images/Simpler.jpg)

#### Other resources to get started

[I Stole a Microsoft 365 Account. Here's How. - YouTube](https://www.youtube.com/watch?v=sZ22YulJwao) by **_John Hammond_**  
[Evilginx for Office 365 step-by-step guide - YouTube](https://www.youtube.com/watch?v=1SqH4n4suX4) by **_Jan Bakker_**  
[ED91 - Office365 Phishing and Bypassing Azure MFA Protection - YouTube](https://www.youtube.com/watch?v=m2xFl1Krspo) by **_Jarno Baselier_**  
[Introduction | Evilginx](https://help.evilginx.com/docs/intro) by **_Kuba Gretzky_**

## 2\. Phishlets

There are a lot of phishlets out there for Microsoft 365. Some still work, some don't. Here is a list of the most recent phishlets that are also actively maintained.

[Microsoft 365](https://github.com/BakkerJan/evilginx3/blob/main/microsoft365.yaml)  
[Microsoft 365 for ADFS](https://github.com/BakkerJan/evilginx3/blob/main/microsoft365-adfs.yaml) (created by [Daniel Underhay](https://research.aurainfosec.io/pentest/hook-line-and-phishlet/))

## 3\. Installation

Evilginx can be set up [locally](https://help.evilginx.com/docs/getting-started/deployment/local) and on a [remote server](https://help.evilginx.com/docs/getting-started/deployment/remote).

[Running Evilginx 3.0 on Windows - JanBakker.tech](https://janbakker.tech/running-evilginx-3-0-on-windows/)  
Here's a cool [batch script](https://gist.github.com/dunderhay/d5fcded54cc88a1b7e12599839b6badb) from Daniel Underhay that will automate your installation on Linux.

Need a VPS? Get yourself a [droplet at DigitalOcean](http://digitalocean.pxf.io/QyLy0P) for only a few bucks per month!  
Need a phishy domain? Get yourself a new [domain at Namecheap](http://namecheap.pxf.io/k0XDAz)!

## 4\. Red-team tips

[How to protect Evilginx using Cloudflare and HTML Obfuscation (jackphilipbutton.com)](https://www.jackphilipbutton.com/post/how-to-protect-evilginx-using-cloudflare-and-html-obfuscation) by **Jack Button**  
[How To: Evilginx + BITB | Browser In The Browser without iframes in 2024 - YouTube](https://www.youtube.com/watch?v=luJjxpEwVHI) and [waelmas/frameless-bitb: A new approach to Browser In The Browser (BITB) without the use of iframes, allowing the bypass of traditional framebusters implemented by login pages like Microsoft and the use with Evilginx. (github.com)](https://github.com/waelmas/frameless-bitb) by **Wael Masri**

## 5\. Defending

[Stop hackers from stealing your Microsoft 365 user's passwords - YouTube](https://www.youtube.com/watch?v=tI1bdVohOK8) by **_Merill Fernando_**  
[AiTM/ MFA phishing attacks in combination with "new" Microsoft protections (2023 edition) (jeffreyappel.nl)](https://jeffreyappel.nl/aitm-mfa-phishing-attacks-in-combination-with-new-microsoft-protections-2023-edt/) by **_Jeffrey Appel_**  
[https://bleekseeks.com/blog/how-to-protect-against-modern-phishing-attacks](https://bleekseeks.com/blog/how-to-protect-against-modern-phishing-attacks) by **_Luke Kavanagh_**  
[Enforce phishing-resistant authentication in Microsoft 365](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-strengths) by **_Microsoft Learn_**  
[Token protection/binding in Entra ID](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-token-protection) by **_Microsoft Learn_**

## 6\. Detecting

[Defending against the Attack of the Clone\[d website\]s! – Thinkst Thoughts](https://blog.thinkst.com/2024/01/defending-against-the-attack-of-the-cloned-websites.html) by **Jacob Torrey**  
[Using honeytokens to detect (AiTM) phishing attacks on your Microsoft 365 tenant – Zolder B.V.](https://zolder.io/using-honeytokens-to-detect-aitm-phishing-attacks-on-your-microsoft-365-tenant/) by **Wesley Neelen**

## 7\. The next big thing: passkeys

[First look: Microsoft Authenticator goes phishing resistant with passkeys (2 min demo) - YouTube](https://www.youtube.com/watch?v=wTLB0Yh70_0)  
[Get started with passkeys in Microsoft 365 - JanBakker.tech](https://janbakker.tech/get-started-with-passkeys-in-microsoft-365/)
