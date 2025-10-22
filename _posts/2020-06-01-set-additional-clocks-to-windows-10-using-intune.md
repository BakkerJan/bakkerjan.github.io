---
title: "Set additional clocks to Windows 10 using Intune"
date: 2020-06-01
categories: 
  - "intune"
  - "windows-10"
tags: 
  - "app"
  - "clocks"
  - "intune"
  - "time"
  - "timezone"
  - "win32"
image: "/assests/images/art-city-clock-clock-face-277458-scaled.jpg"
---

When you work for an international company, or you have to deliver support in other timezones, you might find yourself Googling for time in different timezones from time to time. At least I did.

Then I start looking for a way to make this easier and I was thinking to use BGInfo do reflect the time on my background. When struggling with this for 2 hours, I accidentally stumbled upon this setting, where you can display 2 additional clocks:

- ![](/assets/images/rundll32_ON0YF15jlQ.png)
    
- ![](/assets/images/msedge_uPkPZhTm8y.png)
    

I was very surprised and Tweeted about it. Seems that I was not the only one. This setting seemed undiscovered for many people. I also learned that this setting came with Windows Vista, so it was sitting there the whole time. So now you know!

You can find this feature in the Data & Time settings.

![](/assets/images/image-1.png)

I also learned that you can more clocks in the Alarms & Clock app. You can even pin them to your start menu.

![](/assets/images/image-2.png)

#### How to push it to your devices

So, you have now configured this on your workstation (you're welcome), and you wonder if you can set this up for your other machines. This setting can be configured using registry keys. To see it yourself, configure the clock and check: **Computer\\HKEY\_CURRENT\_USER\\Control Panel\\TimeDate\\AdditionalClocks**

![](/assets/images/image-3.png)

I wrote a small script to set this up. We wrap it up in a Win32 app to push it with Intune, but you can also use the scripts to use with your current management system.

To start, create a folder on your local hard drive, and put in both install.ps1 and uninstall.ps1 files. Also, create an empty output folder. Change the values of both clocks to your needs. If you want to set just 1 clock, you'll have to tweak the script a little bit to delete the code for clock 2.

Enter a **Displayname** and a **Timezone** for both clocks by changing the variables. You'll find all the timezones by using the **tzutil /l** command in your command prompt or by checking the list from Computer\\HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Time Zones. You can also use [this website](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/default-time-zones) as a reference. If your not sure, just set up the clock on your machine, and check the **Computer\\HKEY\_CURRENT\_USER\\Control Panel\\TimeDate\\AdditionalClocks** key.

Your folder would look something like this:

![](/assets/images/image-4.png)

###### Install.ps1

```powershell
<#
Change the values of the clocks to your needs
Edit the Displayname and Timezones for both clocks
If you want to set only one clock, just remove the section from the script. 

Test before use!
This will overwrite the current settings (if set)
#>

#Set DisplayName and TimeZone for additional Clock 1
$Clock1DisplayName = 'Pune'
$Clock1TimeZone = 'India Standard Time'

# Change this!
#Set DisplayName and TimeZone for additional Clock 2
$Clock2DisplayName = 'Melbourne'
$Clock2TimeZone = 'AUS Eastern Standard Time'

#Define path and regkeys
$Clock1 = 'HKCU:\Control Panel\TimeDate\AdditionalClocks\1\'
$Clock2 = 'HKCU:\Control Panel\TimeDate\AdditionalClocks\2\'
$RegKeyPath = 'HKCU:\Control Panel\TimeDate\AdditionalClocks\'

#Set Clock 1

    New-Item -Path $RegKeyPath -Name '1' -Force
    New-ItemProperty -Path $Clock1 -Name 'Enable' -Value '1' -PropertyType DWORD -Force
    New-ItemProperty -Path $Clock1 -Name 'DisplayName' -Value $Clock1DisplayName -PropertyType String -Force
    New-ItemProperty -Path $Clock1 -Name 'TzRegKeyName' -Value $Clock1TimeZone -PropertyType String -Force

#Set Clock 2

    New-Item -Path $RegKeyPath -Name '2' -Force
    New-ItemProperty -Path $Clock2 -Name 'Enable' -Value '1' -PropertyType DWORD -Force
    New-ItemProperty -Path $Clock2 -Name 'DisplayName' -Value $Clock2DisplayName -PropertyType String -Force
    New-ItemProperty -Path $Clock2 -Name 'TzRegKeyName' -Value $Clock2TimeZone -PropertyType String -Force
```

###### Uninstall.ps1

```powershell
<#
Test before use!
#>

#Define path and regkeys
$Clock1 = 'HKCU:\Control Panel\TimeDate\AdditionalClocks\1\'
$Clock2 = 'HKCU:\Control Panel\TimeDate\AdditionalClocks\2\'

#Delete Clock 1

    Remove-Item $Clock1 -Recurse -Force

#Delete Clock 2

    Remove-Item $Clock2 -Recurse -Force

```

#### Wrap the scripts with IntuneWinAppUtil

Use the [https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) to wrap the scripts into a intunewin package.

![](/assets/images/image-5.png)

You'll find the **intunewin** install file in the output folder. We need this for the next step.

Head over to [https://endpoint.microsoft.com](https://endpoint.microsoft.com) to add a new App. Pick a Win32 app and browse for the intunewin installer that we just created. Change the values for the Name, Description, and Publisher.

![](/assets/images/image-6.png)

Enter the install command and uninstall command and make sure you select **User** as the install behavior.

**Install command**: powershell.exe -executionpolicy Bypass .\\install.ps1

**Uninstall command**: powershell.exe -executionpolicy Bypass .\\uninstall.ps1

![](/assets/images/image-7.png)

Select the operating system architecture that you support and pick your minimum operating system version. Optional, add requirements.

![](/assets/images/image-8.png)

To detect if the app is succesfully installed, we check of the register keys are created by adding them to the Detection rules. Add both **Computer\\HKEY\_CURRENT\_USER\\Control Panel\\TimeDate\\AdditionalClocks\\1** and **Computer\\HKEY\_CURRENT\_USER\\Control Panel\\TimeDate\\AdditionalClocks\\2**

![](/assets/images/msedge_VgYH4gPd6m.png)

In this example, I skip the dependencies and assign the app to a test group of devices.

![](/assets/images/image-11.png)

Review the settings and create the app.

![](/assets/images/image-12.png)

Next, head over to your test device and see if the additional clocks show up.

![](/assets/images/image-13.png)

#### Uninstall

The uninstall script will delete both entries (clocks) from the registry, using the uninstall script.

![](/assets/images/image-14.png)

#### Wrap up

I was happily surprised to find this setting, and I think this can be really useful in some cases. As I stated earlier, you can use this script to use with other solutions than Intune. You could also use GPO, startup scripts, and create the solutions that fit your needs.

Happy deploying!
