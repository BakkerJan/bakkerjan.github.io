---
title: "Install Windows Package Manager (winget) using Intune"
date: 2020-05-23
categories: 
  - "intune"
  - "windows-10"
tags: 
  - "appx"
  - "intune"
  - "lob"
  - "windows10"
  - "winget"
coverImage: "vmconnect_qPnLN1LhQw-1.png"
---

Microsoft released a preview of the Windows Package Manager. I'm not going into detail about the product itself, because there are a lot of (better) alternatives for this already in the market. Today, we focus on how to get this tool installed on your endpoints, so you can use it for your software distribution. In this approach I use the APPX package. Normally I would use the Business Store for this, that version does not (yet) contain the winget feature.

For this setup, we need a couple of things:

- The [APPX bundle package](https://github.com/microsoft/winget-cli/releases)
- The Microsoft.VCLibs.140.00 dependency files

Let's start with de dependency files. These are not easy to find. Until I stumbled upon [this post](https://github.com/microsoft/terminal/issues/3097). You can download the files separately using this website: [https://store.rg-adguard.net/](https://store.rg-adguard.net/)

Search for the packages selecting PackageFamilyName as the search type. Look for both **_Microsoft.VCLibs.140.00.UWPDesktop\_8wekyb3d8bbwe_** and **_Microsoft.VCLibs.140.00\_8wekyb3d8bbwe_** to download the files.

![](/assets/images/image-87.png)

You'll end up with a bunch of APPX files. We need these files in the next steps.

![](/assets/images/image-88.png)

## Install the LOB application

Launch the Microsoft Endpoint Manager admin center and go to **Apps->All Apps** and create a new app. Pick the Line of Business app.

![](/assets/images/image-89.png)

Next, select the [Microsoft.DesktopAppInstaller\_8wekyb3d8bbwe.appxbundle](https://github.com/microsoft/winget-cli/releases/download/v0.1.4331-preview/Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.appxbundle) that you downloaded earlier. Browse for the dependency files, so there are added to the installation.

![](/assets/images/image-90.png)

Follow the wizard to assign the application to your users or devices.

- ![](/assets/images/image-91.png)
    
- ![](/assets/images/msedge_KWdi3XccSi.png)
    
- ![](/assets/images/msedge_ca9F4TRnFA-1024x577.png)
    

You can monitor the installation using the device install status page

![](/assets/images/image-93.png)

```
You may need to install the Desktop Bridge VC++ v14 Redistributable Package. This should only be necessary on older builds of Windows if you get an error about missing framework packages.
```

![](/assets/images/vmconnect_cxXCw5PhPm.png)

## Get started with winget

Now the tool is installed on your endpoint you can install packages using PowerShell or command line. To show that it's working I installed VLC player on my demo machine.

![](/assets/images/image-94.png)

To learn more about this new tool, check the following URL's:

- [https://build5nines.com/windows-gets-package-manager-with-winget-cli-utility/](https://build5nines.com/windows-gets-package-manager-with-winget-cli-utility/)
- [https://docs.microsoft.com/en-us/windows/package-manager/winget/](https://docs.microsoft.com/en-us/windows/package-manager/winget/)
- [https://www.thomasmaurer.ch/2020/05/how-to-install-winget-windows-package-manager/](https://www.thomasmaurer.ch/2020/05/how-to-install-winget-windows-package-manager/)
- [https://github.com/microsoft/winget-cli/blob/master/doc/windows-package-manager-v1-roadmap.md](https://github.com/microsoft/winget-cli/blob/master/doc/windows-package-manager-v1-roadmap.md)
