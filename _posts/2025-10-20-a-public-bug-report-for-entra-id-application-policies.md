---
title: "A public bug report for Entra ID application policies"
layout: post
---



![](/images/image-53.png)

Guess what is created? The data type is of type boolean instead!

![](/images/image-54-scaled.png)

Now, I have tried to change it myself, but these attributes, being **security** attributes, some properties are immutable, so I could not change it.

![](/images/image-61.png)

I could instead deactive the old one, and created a new one.

![](/images/image-57-scaled.png)

  
Then I updated my default tenant policy.

![](/images/image-58-scaled.png)

Assigned my new attribute to my managed identity.....

![](/images/image-59-scaled.png)

And it started working right away! Victory at last.

![](/images/image-56-scaled.png)

Now, to make the chaos complete, let's update my policy through the UI (adding another excluded caller). As I learned this should check if the security attribute set exist and try to assign the attribute. It did exaclty that, but then it choked... This makes sense, as the attributes are inactive.

![](/images/image-60-scaled.png)

## Let's wrap up

I think this feature needs some more dev work, and probably another round of testing. This is not Enterprise ready.

Stay safe!
