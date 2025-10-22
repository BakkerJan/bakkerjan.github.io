---
title: "Break glass accounts and Azure AD Security Defaults"
date: 2022-12-21
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-miguel-a-padrinan-2882506-scaled.jpg"
---

Security Defaults is the best thing since sliced bread. I mean, come on! It will enforce MFA for everybody, will block that dirty legacy authentication, and even gives you features that you normally would pay big money for (Azure AD Identity Security). Good enough for a lot of (smaller) organizations out there. Today's post is about that feature and the use of break-glass accounts. For a lot of folks, this post might be obvious, as this is their daily job as a consultant, but I learned that the vast majority of small businesses (and even big ones!) have no clue how this stuff works and where to start. This post is for them.

I was triggered by a document that Microsoft put together with the Australian government, where they give some guidance for small businesses on how to protect their accounts with MFA using Azure Active Directory's Security Defaults feature:  
  
[Technical Example: Multi-Factor Authentication | Cyber.gov.au](https://www.cyber.gov.au/acsc/view-all-content/publications/technical-example-multi-factor-authentication)

The introduction includes steps you should take before you begin: **_Create at least two backup administrator accounts to be used for emergency access and store the credentials offline in a secure location such as a fireproof safe. Only authorized individuals should have access to these credentials._**

Since the document also recommends Security Defaults, there is an interesting scenario to solve. Security Defaults will enforce Azure MFA for **all** users and admins, and you cannot make any exclusions to that. So how do we create an account that can bypass Azure MFA? In my opinion, FIDO2 security keys would be the answer here. When using FIDO for sign-in, the MFA claim would be satisfied, and it gives an easy way to get back into your tenant when needed.

But when we talk about using FIDO2 keys as our break-glass accounts, another thing comes into play: before you can register a FIDO2 security key with your break-glass account, you need to register for MFA first. The chicken and egg problem. Luckily, this problem can be solved with a Temporary Access Pass. See how this relatively easy thing has become pretty complex already? Let's break it down:

1. First, we create one or two accounts with long passwords and assign the Global administrator role.

3. Next, we need to allow FIDO2 keys and Temporary Access Passes in the authentication methods policy.

5. Then we create a Temporary Access Pass for the new account(s).

7. Last but not least, we register the FIDO2 keys using the Temporary Access Pass. Use one FIDO2 key per account. So if you make two accounts, you also should have two keys at hand.

![](/assets/images/Break-galss.png)

## 1\. Create at least one, but preferably two, new accounts.

The easiest way to do this, is by using the Microsoft 365 admin center. Why? It gives you the option to create a strong password that does not expire. If you do this using the Azure portal instead, the password needs to be changed at first sign-in. We don't want that.

In the Microsoft 365 admin center, create a new user. Use a password generator to generate a strong password. I used [Password Generator - Strong, Random Passwords | 1Password](https://1password.com/password-generator/) and created a password of _100_ characters using numbers and symbols. Feel free to use the maximum password length of 256 characters. **Do not store the password. We don't need it!**

Also, make sure to use the \*.onmicrosoft.com domain and that the user is _**not required to change the password**_ when they first sign in.

<figure>

![](/assets/images/image-1.png)

<figcaption>

We don't need a license for these accounts.

</figcaption>

</figure>

<figure>

![](/assets/images/image-2.png)

<figcaption>

Assign the Global Administrator role.

</figcaption>

</figure>

![](/assets/images/image-3.png)

Finish the wizard, and store the username for later use. Repeat the steps for the second account. **Use a different password than the first one.**

![](/assets/images/image-4.png)

## 2\. Allow FIDO2 and Temporary Access Pass

For this step, we move over to the [Azure Portal](https://portal.azure.com/). We need to configure authentication policies to allow the use of FIDO keys and Temporary Access Pass. For better management, create a new security group, and add both break-glass accounts to the new group.

![](/assets/images/image-5.png)

Next, head over to the [authentication policies](https://portal.azure.com/#view/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/~/AdminAuthMethods) blade and enable both FIDO and Temporary Access Pass for the newly created group.

_By the way: in January 2024, the legacy multifactor authentication and self-service password reset policies will be deprecated, and you'll manage all authentication methods using the authentication methods policy. Check out my other blog post if you want to learn more: [Goodbye legacy SSPR and MFA settings. Hello Authentication Methods Policies! - JanBakker.tech](https://janbakker.tech/goodbye-legacy-sspr-and-mfa-settings-hello-authentication-methods-policies/)_

![](/assets/images/2022-12-20-000073.png)

![](/assets/images/2022-12-20-000067-2-1024x624.png)

## 3\. Create a new Temporary Access Pass

Now that we allowed both FIDO2 and Temporary Access Pass, we can create a new Temporary Access Pass for our break-glass accounts.

![](/assets/images/2022-12-20-000068.png)

Capture the Temporary Access Pass for the next step.

![](/assets/images/2022-12-20-000069.png)

## 4\. Use the Temporary Access Pass to register the FIDO2 key

We now have all things in place for the final step of the process: register the key to the account. For that, open up an inPrivate browser session, and browse to [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)  
Enter the username of the break-glass account, and you should be prompted for a Temporary Access Pass.

![](/assets/images/2022-12-20-000070.png)

Use the Temporary Access Pass from the previous step to sign in. If you forgot to capture it, just delete the current one and create a new one.

![](/assets/images/2022-12-20-000071.png)

Add a new method, and select **Security key**. Have your FIDO key ready, and follow the wizard to register the key to the account. During this wizard, you need to create a new PIN for the FIDO key. Make sure you store that PIN in a secure, offline place. When you are done, you can go ahead and delete the Temporary Access Pass. We don't need that anymore.

![](/assets/images/2022-12-20-000072.png)

Do the same for the second account. As I said earlier, use one key per account. Don't add multiple break-glass accounts to one single key.

## Next steps

Now that we have a FIDO key assigned to a break-glass account, you have something to fall back on when things get ugly. The next step would be highly recommended: **monitor the accounts**. Since these accounts have the highest admin privileges, it's good to know when they are used or touched. Imagine someone accidentally deleting the FIDO2 key from the account without knowing. It is therefore recommended to create a routine to test the keys every 90 days or so.

I won't go into details, but there are some excellent community resources out there I can recommend:

[Monitor your Azure AD Break Glass Accounts with Azure Monitor – Daniel Chronlund Cloud Tech Blog](https://danielchronlund.com/2020/01/22/monitor-your-azure-ad-break-glass-accounts-with-azure-monitor/)  
[Monitor Azure AD break-glass accounts with Microsoft Sentinel (jeffreyappel.nl)](https://jeffreyappel.nl/monitor-azure-ad-break-glass-accounts-with-azure-sentinel/)  
[An Azure AD Break Glass Routine Template for your Organization – Daniel Chronlund Cloud Tech Blog](https://danielchronlund.com/2019/09/30/an-azure-ad-break-glass-routine-template-for-your-organization/)

Do know that exporting logs to Log Analytics does require a Premium P1 license. So when you go down that road, also consider using Conditional Access instead of Security Defaults. Needless to say, FIDO2 keys also make outstanding break-glass accounts for tenants that do NOT use Security Defaults.

I hope this post was helpful to you. Stay safe!
