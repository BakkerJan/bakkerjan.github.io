---
title: "A public bug report for Entra ID application policies"
layout: post
---

I've spent the last couple of nights trying out this new feature in Entra ID: application policies. I've already written two ([1](https://janbakker.tech/no-your-nhis-cant-use-passwords-either/),[2](https://janbakker.tech/a-closer-look-at-entra-application-policies-to-govern-secrets-and-certificates/)) blogposts about it, but just when I thought I was done, here's another finding that really blows my mind. Hear me out.

## What's the issue?

According to the docs:  
  
Sometimes, exceptions need to be granted to the user or service creating or modifying the application. For example, imagine an automated process in your organization periodically creates applications and sets passwords on them. You want to block the new passwords in your organization, but you don't want to break this automated process while you're working on updating it. [Application exceptions](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-app-management-policies) wouldn't work in this case, because the apps being created/updated don't exist yet! Instead, you can apply an exception to the process itself.
