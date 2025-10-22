---
title: "Microsoft Secure Score Series – 12 – Turn on customer lockbox feature"
date: 2020-06-14
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "customer-data"
  - "customer-lockbox"
  - "data"
  - "fedramp"
  - "hipaa"
  - "personal"
  - "request"
  - "secure-score"
  - "security"
image: "/assets/images/access-antique-bolt-close-277574-scaled.jpg"
---

## Turn on customer lockbox feature

_Turning on the customer lockbox feature requires that approval is obtained for data center operations that grants a Microsoft employee direct access to your content. Access may be needed by Microsoft support engineers if an issue arises. There's an expiration time on the request and content access is removed after the support engineer has fixed the issue._

Today we are going to talk about the Customer Lockbox feature in Office 365. Some privacy regulations like HIPAA and FEDRAMP require procedures for explicit data access authorization. If your organization needs to comply with this, Customer Lockbox feature is your way to go.

## What is Customer Lockbox?

When you onboard your services to Office 365, you're basically handing over all your documents and emails to Microsoft. Your data is now sitting in Microsoft's datacenter(s), and because Microsoft is taking that very seriously they have a default system called **Datacenter** Lockbox. That means that engineers are fully trained, had several background checks, and must have permission from their manager before they can touch the servers in the datacenter. Engineers are working from isolated Secure Access Workstations and use Multi-factor Authentication all the time.

On top of that, organizations can enable the **Customer** Lockbox feature. This feature comes with the Microsoft and Office 365 E5 license. When this feature is enabled, Microsoft engineers must have approval from the customer's end before they can touch the data.

![](/assets/images/image-52.png)

Assume you have an issue with a Microsoft 365 product. You cannot fix the issue yourself, and decide to open up a support request with Microsoft Support. When the engineer determines that access to the customers' tenant is needed to fix the problem, the following process is kicked off:

The engineer creates a data access request. In that request, the engineer provides a ticket number, the name of the tenant, and the estimated time that is needed to fix the problem.

The request needs to be approved by a Microsoft Support manager.

The request is forwarded to the customer, together with an email to notify the approver.

At the customer's end, the request needs to be approved. This can be done by either a Global Administrator or a Customer Lockbox access approver. Assuming that the request is accepted, the flow continues. The requests need to be approved within 12 hours, otherwise, the request is declined.

An audit record is created

The engineer gets access to the tenant and can fix the issue within the requested timeframe. The duration cannot be longer than 4 hours.

## What is considered customer data?

Microsoft has [several classifications](https://www.microsoft.com/en-us/trust-center/privacy/customer-data-definitions) for data. One of them is customer data. Basically, that is everything that you upload or process through Office 365 services. To give you some examples:

- Emails and attachements
- SharePoint sites and document libraries
- PowerBI datasets and dashboards
- Chat messages
- Voice conversations
- Azure blob storage
- Recorded meetings and Stream video's
- Data in Forms, Planner, Sway and Whiteboard

## Enable Customer Lockbox

You can find the Customer Lockbox setting in the [Microsoft 365 admin center](https://admin.microsoft.com/) under **Settings-> Org settings -> Security & Privacy - Customer lockbox**. Enable the setting "_Require approval for all data access requests_"

![](/assets/images/image-53.png)

## Enable auditing

If you want to keep track of changes, it's recommended to enable auditing in Office 365. Before you enable Customer Lockbox, make sure this is enabled. With auditing enabled, you can track your requests, along with all the other changes in your environment. Turn on auditing using the [Microsoft 365 compliance portal](https://compliance.microsoft.com/auditlogsearch). In the navigation pane, search for Audit and hit the "**Start recording user and admin activity**" button.

![](/assets/images/msedge_DJy1XauBAJ.png)

## Customer Lockbox requests

To approve or deny requests, head over to the [Office 365 admin center](https://admin.microsoft.com/AdminPortal/Home#/homepage) and select **Support -> Customer Lockbox requests**. User with **_Global Admin_** role and **_Customer Lockbox access approver_** role can approve or deny requests.

![](/assets/images/image-55.png)

## Wrap up

This feature will help you to have more control over your data. As I mentioned earlier, some organizations need this level of control to comply with regulatory standards. It will depend on the type of request because many support tickets can be solved **without** access to your data. Microsoft will only have to request access in very rare cases.

So, do I recommend to enable this feature? It depends. In some organizations, there is a gut feeling about the way Microsoft handles your data, despite [the transparency](https://www.microsoft.com/en/trust-center). To take some of that away, you could enable this feature. If you have to comply with regulations, you should enable this right away. On the other hand, when you don't have to deal with regularities, and you don't want to slow down the support dissolution time, you should not bother that much about Microsoft touching your personal data (when needed). It's up to you ;)

Stay safe!
