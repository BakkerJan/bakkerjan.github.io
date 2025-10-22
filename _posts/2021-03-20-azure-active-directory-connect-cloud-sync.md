---
title: "Azure Active Directory Connect - Cloud Sync"
date: 2021-03-20
categories: 
  - "entra"
image: "/assests/images/pexels-omer-aydin-3472690.jpg"
---

When organizations want to extend Active Directory to Azure Active Directory, AD Connect sync is the way to go. AD Connect sync is your go-to feature for all your hybrid workloads, such as identity, domain join, and Exchange.

But as we all know, AD Connect sync is running on a SQL (express) database and is not high-available. For bigger companies with complex requirements, AD Connect sync is the best solution. But there are also companies that don't need AD Connect sync to take advantage of the hybrid identity and password hash sync. Meet Azure AD Connect Cloud Sync. Let's take a look at what it can do, but more important: what it cannot do. Cloud Sync is not for everybody.....

## What is Azure AD Connect Cloud sync

Azure AD Connect Cloud Sync is a new feature to sync attributes from Active Directory to Azure Active Directory without the need to install and maintain AD Connect on-premises. It is a lightweight solution that only needs an Azure AD cloud provisioning agent to build the bridge between both environments. High Availability can be easily done by installing more than one agent. This is very important when organizations rely on password hash synchronization as their primary authentication method. AD Connect will sync hashes every two minutes, so when AD Connect is down, passwords are not updated.

Next, Azure AD Cloud Sync can be used to connect more than one forest. So if you are already using AD Connect sync, you can also extend your installation with AD Connect Cloud Sync.

![Create](/assets/images/existing-forest-new-forest-2.png)

## How to setup Cloud Sync from scratch?

In this scenario, we have a single forest, single domain setup with no AD Connect Sync in place. Let see how easy it is to install AD Connect Cloud Sync.

To get started, go to the Azure management portal and select **Azure Active Directory**. Next, select the Manage **Azure AD cloud sync** hyperlink.

![](/assets/images/image-10.png)

Select Download agent, and agree with the term and conditions to download the installer for the Azure Cloud sync agent.

![](/assets/images/image-11.png)

To install the agent you'll need Windows 2016 or later. Start the installation by executing the installer file. Again, agree with the license term and conditions and click **Install**. You can also do a [scripted installation](https://docs.microsoft.com/en-us/azure/active-directory/cloud-sync/how-to-install-pshell) with PowerShell.

![](/assets/images/image-12.png)

In the first screen, click **Next**.

![](/assets/images/image-13.png)

You will get prompted to enter your Azure AD credentials.

![](/assets/images/image-14.png)

Next, enter your Active Directory credentials to create a Service Account. You can also use a custom group managed service account.

![](/assets/images/image-15.png)

In the next screen, select the directory and domain that you want to connect.

![](/assets/images/image-16.png)

Check the settings, and **Confirm** if everything looks right.

![](/assets/images/image-17.png)

After the installation is done, select **Exit** to finish the wizard. In case you are wondering: By default, cloud sync uses ms-ds-consistency-GUID with a fallback to ObjectGUID as source anchor. There is no supported way to change the source anchor.

![](/assets/images/image-18.png)

Now that the installation is done, go back to the Azure portal to see if the agent shows up. You can install multiple servers if you need to.

![](/assets/images/image-19.png)

Now that we have one agent installed, we can go on and create a new configuration.

![](/assets/images/image-20.png)

Select the Active Directory domain that we connected earlier and click **Create**. Notice that password hash sync is selected and enabled by default. I suggest you leave this enabled.

![](/assets/images/image-21.png)

In the next screen, you can select the scope, similar to ADConnect sync, only a bit limited. You can enable the sync for all users, for selected groups, or for specific Organizational Units (OU's).

![](/assets/images/image-22.png)

In this example, we'll pick the latter one. To get the right OU distinguishedName, we'll use ADSI edit. Here, we only want to sync the objects in the CloudSyncObjects OU.

![](/assets/images/image-23.png)

Paste the distinguishedName and click add. You can add multiple OU's if needed.

![](/assets/images/image-24.png)

If needed, you can adjust the mappings of your users, groups, and contacts. This is a similar experience as the Rule Editor in AD Connect Sync. For now, we leave everything to default.

![](/assets/images/image-33.png)

Next, we are going to provision a single object to test the configuration.

![](/assets/images/image-32.png)

Here, we do the same trick as we did with the OU's. Just copy the distinguishedName of the object with ADSI edit and paste this in the field to

![](/assets/images/image-27.png)

After the provisioning is complete, you can see the details in the right pane. Adjust your settings if needed.

![](/assets/images/image-28-1024x600.png)

In the last step, you are able to set up a notification and configure the threshold to prevent accidental deletion of objects. In step 5, select the Enable toggle and save the configuration.

![](/assets/images/image-29.png)

In the overview page, the configuration will show up as healthy.

![](/assets/images/image-30-1024x324.png)

In the log files, you can check out the objects that are being provisioned by Azure AD Connect Cloud Sync.

![](/assets/images/image-31.png)

## Wrap things up

You might be noticed that we didn't have to set up any SQL (Express) database, and the installation of the agent is just a few clicks and doesn't require complex configuration settings. To sum up the benefits of Azure AD Connect Cloud Sync:

- Azure AD Connect Cloud Sync is _a mouthful_, so you might impress any client or fellow administrator during your daily standup ;)
- It does not require a SQL (Express) database on your server
- By installing multiple agents, you will create high availability without the need to configure inbound ports or load balancers
- The agents will get auto-updated, so no more painful AD Connect swing migrations over the weekends.
- It gives better insights into the sync-logs from the Azure portal
- You can easily force synchronization from the Azure portal (no more Start-ADSyncSyncCicle magic) And also good to know: Cloud provisioning is scheduled to run every 2 mins.
- It can sync up to 50.000 users in a single group

But wait Jan, what are the limitations? It can't be all puppies and sunshine here. You are absolutely right. Let's take a look a the limitations of AD Connect Cloud Sync. Keep in mind that some features may be added later, such as password write-back.

- Cloud Sync does not support Pass-Through Authentication
- Cloud Sync does not support Hybrid Exchange Deployment
- Cloud Sync is not suitable for device sync, so for hybrid join scenario's
- Cloud sync does not support writeback (passwords, devices, groups)

A full comparison between AD Connect Sync and AD Connect Cloud sync can be found [here](https://docs.microsoft.com/en-us/azure/active-directory/cloud-sync/what-is-cloud-sync?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Factive-directory%2Fcloud-sync%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json#comparison-between-azure-ad-connect-and-cloud-sync).

To learn more about Azure AD Connect Cloud Sync, check out these Microsoft Docs sources:

[Azure AD Connect cloud sync FAQ | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/cloud-sync/reference-cloud-sync-faq)  
[What is Azure AD Connect cloud sync. | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/cloud-sync/what-is-cloud-sync?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Factive-directory%2Fcloud-sync%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)  
[Azure AD Connect cloud sync deep dive - how it works | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/cloud-sync/concept-how-it-works)

Stay safe!
