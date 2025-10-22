---
title: "Secure your Azure Management portal"
date: 2020-02-16
categories: 
  - "security"
tags: 
  - "administrators"
  - "azure"
  - "manangement"
  - "portal"
  - "secure"
  - "timeout"
coverImage: "msedge_ArRja1UadG-e1581886836824.png"
---

Today a quick tip to secure your Azure Management Portal. By default, the inactivity timeout of the Azure Management portal is set to _'Never'_. From a security perspective, this is far from ideal. This small setting can be easily overlooked. It's a good idea to configure this for your tenant.

Administrators can set this value themselves. Global administrators are able to set this value globally. When configuring this setting, your administrators will be logged out when they are inactive for a period of time. You can change this setting from the **_Settings_** pane in the Azure portal.

* * *

![An overview of the portal settings](/assets/images/image-2-608x1024.png)

1. Set the time-out on directory level
2. This is the time-out setting per user

![Enable directory level idle timeout](/assets/images/image-3.png)

To set the time-out on directory level, click _**"Configure directory level timeout"**_ from the Settings pane in the Azure portal. Here you can set the value, for example, 30 minutes.

![Override the directory inactivity timeout policy](/assets/images/image-5.png)

New sessions will now honor the new default time-out. Administrators can override this setting, but only with a value less than de directory default.

## Update 16-06-2020 (MCAS Portal)

When using Microsfot Cloud App Security, you can set the time-out for the portal in the settings pane.

![](/assets/images/image-56.png)

* * *

Setting this time-out for your users is another step in the right direction in order to tighten the level of security in your environment. Stay safe!
