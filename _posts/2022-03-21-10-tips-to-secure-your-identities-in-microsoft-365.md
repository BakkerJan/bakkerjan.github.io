---
title: "10 tips to secure your identities in Microsoft 365"
date: 2022-03-21
categories: 
  - "entra"
  - "security"
coverImage: "pexels-life-of-pix-4291.jpg"
---

I was recently invited by the [Dutch Virtual Desktop User Group](https://www.meetup.com/Dutch-Virtual-Desktop-User-Group/events/283235982/) to present a session about Identity Security. Now, because this was in Dutch (my native language), I decided to do a 'short' write-up in English, so everybody can benefit from this. I shared 10 tips on how to secure your identities, but not before I showed how easy it is to phish Office 365 credentials, even if they are protected with MFA.

![](/assets/images/1647290466-scaled.jpg)

You can also check [the recording](https://youtu.be/i67QfxOxTVQ?t=2976) in case you understand (or want to learn) Dutch.

## Set the scene

Now, most attacks start out simple: with a phishing email. And this will happen to the best of us. So during my session, we started off with a real-time hacking demo. Since I had a recording in case something went wrong, I can also show it to you, but you can also check out the [recording](https://youtu.be/i67QfxOxTVQ?t=3431). What you will see:

1. User lands on the phishing page and enters credentials and accepts MFA prompt.
2. Credentals and MFA token are being captured by the [EvilGinx](https://github.com/kgretzky/evilginx2) server.
3. I use Google Chrome and the [Cookie Editor](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en) to inject the token, and take over the session (no MFA required)

https://youtu.be/OA9IQ7j0M58

# 10 tips to secure your identities in Microsoft 365

Now, with this scenario in mind, what can we do as an administrator to prevent this from happening? In the next section, I will share 10 tips on how to strengthen your security posture in Microsoft 365, where we specifically look at identity protection.

## TIP 1 – Block legacy authentication

The most important step to take is to move towards modern authentication. This will allow you to add more layers and really help you to implement the Zero Trust principles. Now, with the front door locked, we should also take care of the backdoor: legacy authentication. With legacy authentication, we are talking about old protocols that can be used to authenticate against Microsoft 365 apps and data, such as IMAP, POP3, and SMTP. These protocols are not capable of enforcing modern authentication such as Multi-factor Authentication. Therefore, they should be blocked.

> Effective October 1, 2022, we will begin to permanently disable Basic Authentication for Exchange Online in all Microsoft 365 tenants regardless of usage, except for SMTP Authentication.
> 
> Microsoft

Now, if you don't want to wait until Microsoft disables legacy authentication for you, you could simply use the new Conditional access templates to Block legacy authentication for your tenant. Using these templates, you can quickly block legacy authentication requests for all users, and therefore reduce the risk of password attacks.

In the Azure portal, go to **Azure Active Directory -> Security -> Conditional Access -> + New policy ->** _Create new policy from templates (preview)_

![](/assets/images/image-16.png)

Next, select the "Block legacy authentication" template, and configure the policy to your needs. It's recommended to always start the policy in "report-only" mode, to measure the potential impact.

![](/assets/images/image-15.png)

If you want to find out if legacy authentication is still used within your environment, the "Sign-ins using legacy authentication" workbook is a good starting point. For a deep dive on this subject, please check out [this massive post](https://www.vansurksum.com/2020/03/01/microsoft-is-going-to-disable-basic-legacy-authentication-for-exchange-online-what-does-that-actually-mean-and-does-that-impact-me/) from fellow MVP Kenneth van Surksum.

![](/assets/images/image-17.png)

## TIP 2 – Do not expire passwords

![](/assets/images/pexels-mikhail-nilov-7534781.jpg)

My next tip will be around password policies. I still see a lot of organizations that force their users to reset their passwords every 90 days, or sometimes sooner. Now, initially, this seems like a good thing to do, but based on research, each time the password is being reset, they become a little weaker. Human beings are not very good at making up and remembering complex passwords, and therefore we use "tricks" like adding numbers or characters at the end of the password. So, a user who used **_MySecurePassword_** for 90 days, and is forced to pick a new one, is more likely to pick **_MySecurePassword1_**, other than making up a completely new password.

So, there is a lot of discussion about password policies, and the question of what "a good password" is. Now, take this scenario as an example: You are working for a company that is using Office 365. The company provided a managed Windows device that is joined to Azure AD, and Single Sign-On is enabled. So, at the start of your workday, you are prompted to enter your password in order to sign in to Windows. Once you signed in, SSO is taking care of the rest so you can use Office 365 without using your credentials. The problem, however, is the fact that you still need to type in your password every day (at least once), and therefore you should remember that password. I have never seen a frontline worker, reach out to their password manager (if they even have one) and type over their password for day-to-day use. Instead, they use a password that they can remember easily. So, that's why you should focus on the places where users need to type a password and offer passwordless options if possible, like Windows Hello for Business, Authenticator App, or FIDO2 keys. Forcing them to frequently change that password, won't make any sense, since they still pick easy-to-remember passwords.

My personal tip to create "good" passwords is to create a passphrase with 3 or 4 random words. These types of passwords are fairly easy to remember, hard(er) to guess, and still more secure than any other password that contains personal information, dates, or names of relatives. Keep in mind that, if you fall for phishing or if your password is being stolen during a breach, [your password does not matter](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984), since it's already known by the attacker. Multi-factor authentication would protect you from worse.

![](/assets/images/image-18.png)

Next to that, you can prevent users from picking easy-to-guess passwords, by implementing Azure AD Password Protection. You can even extend this to your on-premises Active Directory.

![](/assets/images/image-21.png)

You can read more about this topic in a previous post in the Secure Score series: [Microsoft Secure Score Series – 15 – Do not expire passwords - JanBakker.tech](https://janbakker.tech/microsoft-secure-score-series-15-do-not-expire-passwords/)

## TIP 3 – Turn on user risk / sign-in risk policy

The next tip would be around Azure AD Identity Protection. This is an Azure AD Premium P2 feature, but it brings great insight into your identity events. Using the Microsoft intelligence engine, all sign-ins will be measured against a baseline, and if something unusual happens, an automated response takes place. So when a user signs in from an unknown location or device, this will trigger a risk event, that can trigger an MFA prompt, block, or even password reset, depending on your policies. This is sometimes referred to as "risk-based MFA", and only prompts users when necessary. This will prevent users from accepting numerous MFA prompts, without knowing where it comes from (MFA fatigue).

You can integrate this feature with Conditional Access so that you have granular control of your policies. In short, you can use these policies in three ways:

1. Using the Azure AD Identity Protection policies (not able to define conditions and exclusions)
2. Using the Conditional Access sign-in and user-risk conditions (more flexibility)
3. By enabling Security Defaults (no P2 license required, but also no flexibility)

<figure>

![](/assets/images/image-22.png)

<figcaption>

With Security Defaults, you basically get "free P2 features"

</figcaption>

</figure>

If you want to learn more about Azure AD Identity Protection, and the integration with Conditional Access, please reach out to these resources:

[Microsoft Secure Score Series – 11 – Turn on user risk policy - JanBakker.tech](https://janbakker.tech/microsoft-secure-score-series-11-turn-on-user-risk-policy/)  
[Microsoft Secure Score Series – 07 – Turn on sign-in risk policy - JanBakker.tech](https://janbakker.tech/microsoft-secure-score-series-07-turn-on-sign-in-risk-policy/)  
[Close the gap. Azure AD Identity Protection & Conditional Access. - JanBakker.tech](https://janbakker.tech/close-the-gap-azure-ad-identity-protection-conditional-access/)

## TIP 4 – Enable password hash sync

Password hash synchronization is one of the sign-in methods used to accomplish a hybrid identity. Azure AD Connect synchronizes a hash, of the hash, of the user’s password from an on-premises Active Directory instance to a cloud-based Azure AD instance. Password hash synchronization helps by reducing the number of passwords your users need to maintain to just one. Enabling password hash synchronization also allows for leaked credential reporting.

![](/assets/images/azure-ad-authn-image2.png)

In the previous tip, we talked about Identity Protection which can discover various risks in your environment. One of those risk detections is "leaked credentials". If you use cloud identities, no further action is needed, but if you use hybrid identities (most of us do that right?) you need to sync your password hashed in order to benefit from this feature.

This risk detection type indicates that the user's valid credentials have been leaked. When cybercriminals compromise valid passwords of legitimate users, they often share those credentials. This sharing is typically done by posting publicly on the dark web, paste sites, or by trading and selling the credentials on the black market. When the Microsoft leaked credentials service acquires user credentials from the dark web, paste sites, or other sources, they are checked against Azure AD users' current valid credentials to find valid matches.

![](/assets/images/image-24.png)

If you want to learn more about this, please reach out to this blog post: [Microsoft Secure Score Series – 03 – Enable Password Hash Sync if hybrid - JanBakker.tech](https://janbakker.tech/microsoft-secure-score-series-03-enable-password-hash-sync-if-hybrid/)

## TIP 5 – Pink thumb extension

Now, as we stated before, most breaches start with phishing. A nifty way to prevent this is by using this Edge/Chrome extension and teaching your users to pay attention when entering credentials.

How does this work? When a phishing link is clicked and asks for credentials, the thumb icon will be greyed out. When the page is legit, the pink thumb will show up. So, the users will only provide credentials when the icon is pink.

![](/assets/images/image-1-1.png)

![](/assets/images/image-2-1.png)

This will of course not prevent you from all phishing attacks, but it's a very user-friendly (free) feature to add to your companies' cyber security toolkit and training sessions.

More info can be found here: [Pink Thumb | (emptydc.com)](https://emptydc.com/2021/06/22/pink-thumb/)

![](/assets/images/image.png)

<figure>

![](/assets/images/image-23.png)

<figcaption>

Grabbed from the creators' website

</figcaption>

</figure>

_**Update 3/31/2022.**_ When I posted this on Twitter, I got some really good feedback about this tip. Please read the Twitter thread to learn the downside of Visual Security Indicators, or read this article from the master himself. [Troy Hunt: The Decreasing Usefulness of Positive Visual Security Indicators (and the Importance of Negative Ones)](https://www.troyhunt.com/the-decreasing-usefulness-of-positive-visual-security-indicators-and-the-importance-of-negative-ones/)

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">10 tips to secure your identities in Microsoft 365<a href="https://t.co/F39PD0cFOD">https://t.co/F39PD0cFOD</a></p>— Jan Bakker (@janbakker_) <a href="https://twitter.com/janbakker_/status/1506007487509667840?ref_src=twsrc%5Etfw">March 21, 2022</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## TIP 6 – Defender for Cloud Apps

In order to take full advantage of Azure AD Identity Protection, you should enable the integration with Defender for Cloud Apps (previously known as Microsoft Cloud App Security). A lot of [risk signals](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk) can only be detected by Defender for Cloud Apps:

- Suspicious inbox manipulation rules
- Impossible travel
- New country
- Activity from anonymous IP address
- Suspicious inbox forwarding
- Mass Access to Sensitive Files

Defender for Cloud Apps has an integrated automated response module, where you can trigger Power Automate flows to take automated actions, send emails, or even text messages.

![](/assets/images/image-26.png)

To enable Azure AD Identity Protection alert integration, go to your Defender for Cloud Apps portal ([https://aka.ms/mcasportal](https://aka.ms/mcasportal)) and go to **Settings**.

![](/assets/images/image-28.png)

Under the **Threat Protection** section, you will find the Azure AD Identity Protection setting:

![](/assets/images/image-25.png)

Learn more: [Integrate Azure Active Directory Identity Protection with Defender for Cloud Apps | Microsoft Docs](https://docs.microsoft.com/en-us/defender-cloud-apps/aadip-integration)

## TIP 7 – Azure MFA additional context & Sign-in frequency

I'm a big fan of the Microsoft Authenticator app for its simplicity and user-friendly design. Users can simply approve sign-in requests that are protected with Azure MFA. However, there lurks danger behind this feature. Many companies are "over-prompting" their users so it is not always clear where these prompts come from.

![](/assets/images/image-29.png)

In general, you should only be prompted once per user, per device, and per password reset, but this really depends on the configuration of your Azure MFA settings. This goes hand in hand with the settings around the persistent browser, sign-in frequency, and Keep Me Signed In feature. I wrote a full post about this earlier: [Sure, keep me signed in! And don't prompt for MFA! - JanBakker.tech](https://janbakker.tech/sure-keep-me-signed-in-and-dont-prompt-for-mfa/)

Now, there is a [very nice article on the Microsoft docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime) that gives best practices around this topic. In short, this is the take-away:

**If you have an Azure AD Premium license:**

- Enable single sign-on (SSO) across applications using [managed devices](https://docs.microsoft.com/en-us/azure/active-directory/devices/overview) or [Seamless SSO](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sso).
- If reauthentication is required, use a Conditional Access [sign-in frequency policy](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).
- For users that sign in from non-managed devices or mobile device scenarios, persistent browser sessions may not be preferable, or you might use Conditional Access to enable persistent browser sessions with sign-in frequency policies. Limit the duration to an appropriate time based on the sign-in risk, where a user with less risk has a longer session duration.

**If you have Microsoft 365 apps licenses or the free Azure AD tier:**

- Enable single sign-on (SSO) across applications using [managed devices](https://docs.microsoft.com/en-us/azure/active-directory/devices/overview) or [Seamless SSO](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sso).
- Keep the _Remain signed-in_ option enabled and guide your users to accept it.

> "Our research shows that these settings are right for most tenants. Some combinations of these settings, such as _Remember MFA_ and _Remain signed-in_, can result in prompts for your users to authenticate too often. Regular reauthentication prompts are bad for user productivity and can make them more vulnerable to attacks."
> 
> Microsoft

Now that we know how to configure the optimal policies (for most organizations), there are two very interesting features that can help to tighten the security around the Microsoft Authenticator App.

**Additional Context**

This feature will add additional context to the MFA prompt in the Authenticator App, such as the name of the application, and the sign-in location based on IP address. If those details seem unfamiliar to the users, they are more likely to deny the request.

![](/assets/images/image-30.png)

**Number matching**

Instead of prompting your users to approve or deny, it is also possible to require number matching. The sign-in screen will show a number, that needs to be typed within the Authenticator app on the phone. If those numbers match, the sign-in is approved. In case of an attack, the user would not be able to approve the MFA request, since it does not know the number to type.

![Screenshot of user entering a number match.](/assets/images/phone-sign-in-microsoft-authenticator-app.png)

Both features can be enabled rather simply. Check out this article on how to do that. [Enable Location Information and Code Match for Azure MFA - JanBakker.tech](https://janbakker.tech/enable-location-information-and-code-match-for-azure-mfa/)

## TIP 8 – Defender for Office 365

As stated in the intro, most attacks start with phishing. There is a lot that you can do on the prevention side here, and it is all built-in in Defender for Office 365.

I want to point out three features that are key in protecting your users from unwanted content.

- Attack Simulator
- Safe Links
- Anti phishing policies

The best way to see an overview of the products and their license requirements is to get an overview from [Home | M365 Maps](https://m365maps.com/)

![](/assets/images/Microsoft-Defender-for-Office-365.png)

#### Attack simulator

This tool can be used to train your users in identifying phishing and get them educated on that subject. Administrators can send out phishing emails that look very tempting to check if users will take the bait, or report them. Based on the outcome, you can provide a follow-up, like an e-learning module.

![](/assets/images/image-31.png)

#### Safe Links

Safe Links is a feature that is able to do time-of-click verification of URLs and links in email messages and other locations.  Safe Links protection for Office 365 apps is available in supported desktop, mobile, and web apps. So, when a user did not pay attention and clicks a malformed link, Safe Links is able to block that action.

#### Anti-phishing policies

This feature is very good in detecting phishing attacks and can be used to give guidance to your users by providing safety tips, like this example:

![First contact safety tip for messages with one recipient.](/assets/images/safety-tip-first-contact-one-recipient.png)

It can also be used to detect impersonation. Impersonation is where the sender or the sender's email domain in a message looks similar to a real sender or domain. It is very common to impersonate managers, board members, or chiefs within an organization, so those policies can be scoped to specific users as well.

## TIP 9 – Privileged Identity Management

Zero Trust is based on the principles of **_'least privilege_'** and **'_always verify'_**. Azure AD resources and roles are often accessible with standing access by IT staff, or sometimes even office workers. In most situations, this access is never been reviewed, even when users switch jobs, or leave the company. Those who read my blog posts more often, do know that I'm a big fan of the Identity Governance features in Azure AD. It is a really powerful feature to keep in control of your role and resource access.

![](/assets/images/EMS-Enterprise-E5-1024x1024.png)

Using Azure AD Privileged Identity Management, administrators are able to access roles and resources, just in time, and with just enough privileges. Access can be protected with MFA or approval from a manager and can be reviewed on a recurring base. Together with Conditional Access, high privileged roles can be protected in such a way that they are only usable from managed devices (Privileged Access Workstations) or locations.

I have written a lot of articles about this topic, so if you want to learn more, please check out these posts as well:

[Role Assignable Groups and Privileged Identity Management. - JanBakker.tech](https://janbakker.tech/role-assignable-groups-and-privileged-identity-management/)  
[Privileged Identity Management Discovery and insights - JanBakker.tech](https://janbakker.tech/privileged-identity-management-discovery-and-insights/)  
[Azure Active Directory Identity Governance – Azure AD Entitlement Management - JanBakker.tech](https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/)  
[Azure Active Directory Identity Governance – Privileged Identity Management - JanBakker.tech](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/)  
[Azure Active Directory Identity Governance - Access Reviews - JanBakker.tech](https://janbakker.tech/active-directory-identity-governance-access-reviews/)  

## TIP 10 – Go Passwordless!

Last but not least: the best tip against phishing is related to the source of all evil: passwords. We need to get rid of them. Simply put: you cannot steal a password if there ain't any.

Microsoft is really determined to play a big role in that mission and offers the following three passwordless authentication options that integrate with Azure Active Directory (Azure AD):

- Windows Hello for Business
- Microsoft Authenticator app
- FIDO2 security keys

#### Windows Hello for Business

Windows Hello for Business is ideal for information workers that have their **own** designated Windows PC. The biometric and PIN credentials are directly tied to the user's PC, which prevents access from anyone other than the owner. With public key infrastructure (PKI) integration and built-in support for single sign-on (SSO), Windows Hello for Business provides a convenient method for seamlessly accessing corporate resources on-premises and in the cloud.

#### Microsoft Authenticator app

You can also allow your employee's phone to become a passwordless authentication method. You may already be using the Microsoft Authenticator App as a convenient multi-factor authentication option in addition to a password. You can also use the Authenticator App as a passwordless option.

The Authenticator App turns any iOS or Android phone into a strong, passwordless credential. Users can sign in to any platform or browser by getting a notification to their phone, matching a number displayed on the screen to the one on their phone, and then using their biometric (touch or face) or PIN to confirm.

#### FIDO2 security keys

FIDO2 security keys are an unphishable standards-based passwordless authentication method that can come in any form factor like USB keys, or even wearables. Users can register and then select a FIDO2 security key at the sign-in interface as their main means of authentication. These FIDO2 security keys are typically USB devices, but could also use Bluetooth or NFC.

FIDO2 security keys can be used to sign in to their Azure AD or hybrid Azure AD joined Windows 10 devices and get single-sign on to their cloud and on-premises resources. Users can also sign in to supported browsers. FIDO2 security keys are a great option for enterprises who are very security-sensitive or have scenarios or employees who aren't willing or able to use their phones as a second factor. FIDO2 keys are perfect for shared-PC scenarios like call centers, schools, or universities.

Passwordless starts with the user onboarding. New hires or students can register their passwordless credentials using a temporary access pass, without even knowing their real password. If you want to learn more about passwordless methods and how to use temporary access passes, please check out these articles:

[Onboard FIDO2 keys using Temporary Access Pass in Azure AD - JanBakker.tech](https://janbakker.tech/onboard-fido2-keys-using-temporary-access-pass-in-azure-ad/)  
[This might be the FIDO2 key for you! Authentrend ATKey.Pro - JanBakker.tech](https://janbakker.tech/this-might-be-the-fido2-key-for-you-authentrend-atkey-pro/)  
[Azure Active Directory Temporary Access Pass - JanBakker.tech](https://janbakker.tech/azure-active-directory-temporary-access-pass/)  
[Azure Active Directory passwordless sign-in | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-passwordless)

## Let's wrap up

As you can see, there is a lot to think about when it comes to securing identities. With these 10 tips, you can start strengthening your security posture with native Azure AD (premium) features. If you have any questions regarding these topics, feel free to reach out.

Stay safe!
