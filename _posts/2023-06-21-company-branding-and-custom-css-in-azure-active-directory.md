---
title: "Company branding and custom CSS in Azure Active Directory"
date: 2023-06-21
categories: 
  - "entra"
image: "/assets/images/msedge_50zG2Xq4m7.png"
---

Company branding in Azure AD is a nice feature that allows administrators to prettify the sign-in experience for their end-users. It also comes with the possibility of ingesting custom CSS code.

A client recently moved from ADFS to Azure AD, and they wanted to update the sign-in screen to look more like the good old ADFS theme. Now, this is pretty easy to do, but by default, the background image comes with an overlay to improve contrast and legibility.

![](/assets/images/image-74.png)

Time for me to dive into the [CSS template](https://download.microsoft.com/download/7/2/7/727f287a-125d-4368-a673-a785907ac5ab/custom-styles-template-013023.css) and find the corresponding CSS class. It was pretty easy to find, and the [CSS template](https://download.microsoft.com/download/7/2/7/727f287a-125d-4368-a673-a785907ac5ab/custom-styles-template-013023.css) is rich in comments.

![](/assets/images/image-75.png)

The code I added will remove the overlay:

```
background: rgba(0, 0, 0, 0%);
```

![](/assets/images/image-76.png)

Next, you can upload the CSS file in the [Company Branding settings](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/LoginTenantBranding). It can take up to 15 minutes before the changes are visible. A quick tip to see the result is to open this smart link in a private session: _https://portal.office.com/?domain\_hint=<yourdomain>_

<figure>

![](/assets/images/msedge_CkmSLXMQ9D-1024x607.png)

<figcaption>

Default overlay

</figcaption>

</figure>

<figure>

![](/assets/images/msedge_50zG2Xq4m7-1024x607.png)

<figcaption>

Overlay 0%

</figcaption>

</figure>

If you like to test your changes first, you can easily edit your CSS using the Developer tools in the browser. Changes will be visible straight away.

![](/assets/images/image-77.png)

Now, this is not a post to convince you to remove the overlay. It's a matter of personal taste, and in some cases, the overlay will give just the right amount of contrast to make it more pretty.

Let's see what happens when we remove the overlay for the tenant of our mothership Microsoft. What do you think?

![](/assets/images/msedge_AznuCYVVhJ-1024x607.jpg)

![](/assets/images/msedge_nK2C0Yu0WW-1024x607.jpg)

## Take it to the next level!

Now, there is a lot you can do with custom CSS. You can go nuts if you want. I'd like to keep things simple, but some of you are here for the looks.😊 Take a look at the documentation on what you can and cannot do:

[CSS reference guide for customizing company branding - Azure AD - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/reference-company-branding-css-template)

Also, check out this post by Thibault for inspiration!  
[How to customize your Azure AD sign-in page with enhanced capabilities | Thibault Joubert (thijoubert.com)](https://www.thijoubert.com/2023-02/AzureAD_LoginPage/)

CSS template can be downloaded here:

[https://download.microsoft.com/download/7/2/7/727f287a-125d-4368-a673-a785907ac5ab/custom-styles-template-013023.css](https://download.microsoft.com/download/7/2/7/727f287a-125d-4368-a673-a785907ac5ab/custom-styles-template-013023.css)

Stay cool!
