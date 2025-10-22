---
title: "Entra ID Dynamic Groups - Direct reports of a manager"
date: 2024-05-27
categories: 
  - "entra"
image: "/assets/images/pexels-fauxels-3184302-scaled.jpg"
---

Here's a quick tip that I discovered only recently. A nice, somehow hidden feature of Entra ID dynamic groups is the possibility of creating a dynamic group for the reports of a specific manager. When the manager's direct reports change in the future, the group's membership is adjusted automatically.

Assume you want to create a dynamic group that holds all the direct reports of Miriam Graham; here's how to do it.

From the Entra admin center, go to **Identity > Groups > All groups**. Create a new group, name it, and pick the _dynamic user_ membership type.

![](/assets/images/image-3.png)

Now, here's the trick. In the Rule syntax, add this syntax, where <id> is the objectID of the manager.

```
Direct Reports for "manager objectid"
```

![](/assets/images/image-4.png)

The objectID can be found on the user's overview page.

![](/assets/images/image-5.png)

After a short while, the group is populated, and all direct reports of Miriam are dynamically added to it.

Needless to say, in order for this to work, the manager object of the user needs to be populated.

![](/assets/images/image-6.png)

Here's the Microsoft Learn page: [Rules for dynamically populated groups membership - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/users/groups-dynamic-membership#create-a-direct-reports-rule)

Stay safe!
