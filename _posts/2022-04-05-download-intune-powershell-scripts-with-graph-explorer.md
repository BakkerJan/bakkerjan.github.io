---
title: "Download Intune PowerShell scripts with Graph Explorer"
date: 2022-04-05
categories: 
  - "entra"
  - "intune"
coverImage: "pexels-miguel-a-padrinan-2882550.jpg"
---

This quick post will show an easy method to fetch your PowerShell scripts after you have uploaded them using the Intune management portal. Unfortunately, the portal does not provide a UI to download the script content as soon as you hit that save button.

![](/assets/images/image.png)

## Graph Explorer to the rescue

There are multiple ways to do this using PowerShell scripts. If you want to bulk download all the scripts in your tenant, I recommend using [this method](https://oliverkieselbach.com/2020/02/06/get-back-your-intune-powershell-scripts/), created by Oliver Kieselbach. This module is also capable of downloading proactive remediation scripts from Intune.

The method I want to show today does not involve (complex) PowerShell scripts. The only thing you need is a browser. Head over to [https://aka.ms/ge](https://aka.ms/ge) and sign in with your admin account. Use the following request to pull out all the scripts from your tenant:

```
https://graph.microsoft.com/beta/deviceManagement/deviceManagementScripts
```

To run this request, you'll need the **_DeviceManagementConfiguration.Read.All_** permission.

![](/assets/images/image-1.png)

After running the request, you will find all the scripts in your tenant. To get the script's content, you'll need to grab the ID. You can use the CTRL+F (search) feature to quickly find the script you are looking for if you have a lot of output.

![](/assets/images/image-3.png)

You can also find this ID using the Microsoft Endpoint Manager admin center; that's probably the easiest way.

![](/assets/images/image-2-1024x496.png)

Now that you have grabbed the ID, you need to run the following request, where you replace {deviceManagementScriptId} with your ID:

```
https://graph.microsoft.com/beta/deviceManagement/deviceManagementScripts/{deviceManagementScriptId}
```

This will get the details of your script and does also include the script content.

![](/assets/images/image-4.png)

Now, as you probably noticed, the script content is encoded.

Copy the value of the scriptContent parameter, and paste this into the decoder of your choice, Notepad ++, for example.

![](/assets/images/image-6.png)

![](/assets/images/1649167670-1024x717.png)

You can also use one of the free online converters, such as [Base64 Decode and Encode - Online](https://www.base64decode.org/)

![](/assets/images/image-5.png)

The same works for custom detection scripts in Intune Win32 apps by running:

```
GET https://graph.microsoft.com/beta/deviceAppManagement/mobileApps/{id} You can find the encoded string under rules > scriptcontent. 
```

![](/assets/images/image-3.png)

It also works for proactive remediation scripts using these API calls for both detection and remediation script:

```
https://graph.microsoft.com/beta/deviceManagement/deviceHealthScripts/{id}?$select=detectionScriptContent

https://graph.microsoft.com/beta/deviceManagement/deviceHealthScripts/{id}?$select=remediationScriptContent
```

Easy right?

Stay safe!
