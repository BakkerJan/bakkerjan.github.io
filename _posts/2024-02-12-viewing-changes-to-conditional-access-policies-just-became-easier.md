---
title: "Viewing changes to Conditional Access policies just became easier!"
date: 2024-02-12
categories: 
  - "entra"
  - "knowledgebase"
  - "security"
image: "/assets/images/image-33.png"
---

Today, a quick tip for all Entra admins out there. Conditional Access policies can be subject to change. When a policy is changed, its not very easy to see what changed. From the audit logs, this is how it looks:

![](/assets/images/image-30.png)

Let's face it; that's not very convenient to read. Well, here's the good part: Microsoft released a new feature that can "visualize" changes to Conditional Access straight from the audit logs.

![](/assets/images/image-31.png)

This will show the changes side by side.

![](/assets/images/image-32.png)

You can also switch to Inline mode.

![](/assets/images/image-33.png)

From the [audit logs](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/AuditLogList.ReactView) in the Microsoft Entra admin center, select the filter **Category**: _'Policy'_ and **Activity**: _'Update conditional access policy_'. The new feature can be found on the Modified Properties tab.

![](/assets/images/image-34.png)

That's it!

Stay safe.
