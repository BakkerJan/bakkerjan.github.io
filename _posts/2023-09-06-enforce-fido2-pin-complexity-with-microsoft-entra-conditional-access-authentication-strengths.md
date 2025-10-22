---
title: "Enforce FIDO2 PIN complexity with Microsoft Entra Conditional Access Authentication Strengths."
date: 2023-09-06
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-bella-white-622134-scaled.jpg"
---

As you may or may not know, most FIDO2 security keys can be set up with easy PINs like 1111 or 123456. Just like passwords, users tend to come up with easy-to-remember PINs.

Token2 recently [announced](https://www.token2.com/site/page/blog?p=posts/70) their PIN+ series, a line of FIDO2 Security keys. These security keys feature advanced PIN complexity rules that set a new standard for security. PIN+ keys implement specific complexity rules for both numeric and alphanumeric PINs, which can be found [here](https://www.token2.com/site/page/blog?p=posts/70).

With the use of Microsoft Entra Conditional Access Authentication Strengths, you can enforce a FIDO2 key with PIN complexity for specific scenarios like:

- Privileged Admin roles

- High Privileged applications

- Break-Glass emergency accounts

- Priority users (C-level)

In this blog post, we take a look at Megan Bowen's account. She has 2 FIDO2 keys registered to her account. One is a FIDO2 key that allows a simple PIN, and the other one is the new [Token2 T2F2-PIN+/TypeC](https://www.token2.com/shop/product/token2-t2f2-pin-typec-fido2-u2f-and-totp-security-key-with-pin-complexity-feature)

I have configured a custom Authentication strength and added the unique AADGUID of the key to that policy. The AADGUID is **_ab32f0c6-2239-afbb-c470-d2ef4e254db7_**

_An important note is that most vendors use the same AADGUID for all keys. That makes it harder to enforce a single type of key but is helpful in scenarios where you want to ensure only keys from a specific vendor are used._

![](/assets/images/image.png)

Next, I created a Conditional Access policy to enforce authentication strengths for Megan when using any of the Microsoft admin portals.

![](/assets/images/image-1.png)

In the Acces controls section, I picked **_"Require authentication strength"_** and selected the authentication strength I just created.

![](/assets/images/image-2.png)

## End-user experience

Now, Megan has multiple FIDO keys registered on her account. When she tries to access the Microsoft Entra portal using a non-allowed FIDO key, access is blocked.

![](/assets/images/image-4.png)

This will also show in the sign-in logs under the Conditional Access Policy details.

![](/assets/images/image-3.png)

Next, Megan tries to sign in with her new FIDO key, with the PIN complexity enforced. This is now allowed, which will also show in the logs.

![](/assets/images/msedge_OvUx4dg3bR.png)

## Closing thoughts

Enforcing a complex PIN on your FIDO key might not always be a good idea. I would not recommend this for regular end-users as we want to keep things simple and user friendly, but this can be considered for the more advanced use cases where you want to enforce a stronger security policy. That said, this demo also shows how powerful the Authentication Strength feature in Conditional Access is. Together with the Authentication Context feature you can cover a lot of scenarios these days.

Stay safe!
