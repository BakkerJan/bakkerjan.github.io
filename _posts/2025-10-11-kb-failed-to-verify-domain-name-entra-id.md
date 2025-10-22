---
title: "KB - Failed to verify domain name - Entra ID"
date: 2025-10-11
categories: 
  - "entra"
  - "knowledgebase"
image: "/assets/images/image-19-scaled.png"
---

This is a knowledge base item. Hope it will help you someday.

## The issue

When adding a custom domain to Entra ID, you are unable to get it verified. The error:

_Unable to verify domain name. Ensure you have added the record above at the registrar for 'yourdomain.com', and try again in a little while. Click here for more information._

Failed to verify domain name 'yourdomain.com'

![](/assets/images/image-16.png)

![](/assets/images/image-17.png)

## The solution

Now, assuming you've added the correct DNS record (which can be tricky sometimes), the error doesn't reveal much detail here. And that's why we need to dig a bit deeper to understand the exact reason.

Try verifying the domain again, but now with your browser **developer tools** running. Make sure it records network events, which includes API calls.

![](/assets/images/image-18.png)

Now, explore the response of that API call, and it will tell you the exact reason why verification failed.

![](/assets/images/image-20-scaled.png)

If you see something like this:

```
        "errorDetail": "Domain verification failed with the following error: 'Error in DNS verification. code=InternalError'."
```

Reach out to your DNS registar for help, because you either did not add the record, or you misconfigured it.

In my case, my domain was already added to another tenant.

![](/assets/images/image-3.png)

If this is the case, you probably want to find out in wich tenant your domain is registered. That can be done using this website:

[Find your Microsoft Azure and Office 365 tenant ID - What is my tenant ID?](https://www.whatismytenantid.com/)

![](/assets/images/image.png)

Hope that helps!

If not, you learned a new skill today. Never let the Entra portal fool you; always peek behind the curtain.

Stay safe!
