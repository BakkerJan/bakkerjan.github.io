---
title: "KB - Add account operation is blocked by policy on the device"
date: 2021-10-02
categories: 
  - "knowledgebase"
image: "/assets/images/pexels-brett-sayles-2821220.jpg"
---

This is a knowledgebase item. Hope it helps you out someday.

## Error

Add work or school account in Windows 10 or 11 fails with this message: "add account operation is blocked by policy on the device". Error code: CAA50101

![](/assets/images/1633152189.png)

## Solution

Check the value of Computer\\HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Policies\\Microsoft\\Windows\\WorkplaceJoin\\BlockAADWorkplaceJoin

Change the value to 0.

![](/assets/images/image-6.png)

If this device is managed by your organization, you might not able to change this. Please contact your administrator, if that is the case.
