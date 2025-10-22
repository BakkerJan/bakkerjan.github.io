---
title: "Azure AD tenant branding; size does matter!"
date: 2020-02-26
categories: 
  - "entra"
tags: 
  - "azure-ad"
  - "background-image"
  - "tenant-branding"
  - "tinypng"
image: "/assests/images/Upcoming-changes-to-the-Azure-AD-signin-experience-1.png"
---

Earlier today, I read this article from Alex Simons about the [change that is coming to the Azure AD sign-in experience](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-the-azure-ad-sign-in-experience/ba-p/1185161). In this change the background image of the login screen is being replaced for a smaller one, so the page loads faster. Good news for the low bandwidth offices out there! The article states: _If you’ve configured a custom background image in Company Branding for your tenant there is no change to your users._ That got me thinking.

When I configure tenant branding on an Azure AD tenant, I often struggle to get the right image and file size. In most organizations the company logo images and wallpapers are huge in size, so you got to scale them down in order to fit them into the Azure AD branding page. As you might know, the prerequisites for the background image are:

- **_1920 x 1080 pixels_**
- **_less than 300 kb in size_**

Getting the right image size is not the hardest part. You just open the image in your favorite editing app, say Paint 3D, and you scale the canvas and the image to the right size. Most of the time, you end up with a pretty large file size, way bigger than 300kb.

I checked the background image on my tenant and it turned out to be **138.7 kb**. The easiest way to see this is by using the developer mode in your Edge browser. Use a guest session in order to stop SSO from happening. Open up _**https://login.microsoftonline.com**_ and type in your username so you'll be redirected to your branded page. In the menu, look for **More tools** -> **Developers tools** or use _CRTL-SHIFT-I_

Then move over to the network tab, and filter for the word **_branding_**.

![](/assets/images/image-18-1024x495.png)

Here you will find your logo and background image. I got mine shrunk to **53.2 kb** as you can see. Page loads a little bit quicker now!

I am sure there are a lot more or even better ways to do this, but I used the website [https://tinypng.com/](https://tinypng.com/) to shrink my image. Just upload your background image and let the panda to the magic. My image was shrunk with a whopping 62%.

![](/assets/images/msedge_ACdXxbfhwA-1024x502.png)

So even when this will not have any effect on your Secure Score, I would highly recommend taking a look at this. User experience is important!
