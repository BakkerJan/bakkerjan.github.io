---
title: "Automate issuing Temporary Access Pass for joiners with LifeCycle Workflows"
date: 2022-10-03
categories: 
  - "entra"
  - "logic-apps"
image: "/assests/images/pexels-ketut-subiyanto-4559596-scaled.jpg"
---

On September 30th, 2022, [Pim Jacobs](https://twitter.com/pimjacobs89) and I did a session on the brand new Lifecycle Workflows feature in Azure AD Identity Governance. During that session, I did a demo showing the integration with Logic Apps. Using this extension, I could use the Graph API to create a new Temporary Access Pass for a new hire, 7 days before the first workday. This post will describe the steps to build the solution.

## Introduction to LifeCycle Workflows

First, let us quickly talk about the new feature. Lifecycle Workflows in Azure AD Identity Governance will bring automation capabilities to the Joiner-Mover-Leaver processes in your organization.

Workflows can have one or more tasks that can be scheduled or run on demand. All scheduled runs are based on the EmployeeHireDate and EmployeLeaveDateTime attributes in Azure AD. To give you an idea, here is an example of a workflow.

**Task**: enable the account, send welcome mail, and add the user to the corporate team in Teams.  
**When** (trigger): Seven days before the NewEmployeeHireDate attribute value.  
**Who** (scope): new hires in the Sales department.

Lifecycle Workflows can be extended to Logic Apps using the Custom extensions feature. In this blog post we are taking a look at one of the use cases that we can solve with Logic Apps.

If you want to learn more about this new feature, please check: [What are lifecycle workflows? - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/what-are-lifecycle-workflows)

## Solution overview

Let's have a look at the overview of the workflow that we are building today. 7 days prior to the EmployeeHireData, a new Temporary Access pass will be created using a Logic App. Next, an email will be sent to the new hire and their manager. The Temporary Access Pass itself, will be sent using SMS to the new hire.

![](/assets/images/TAP-LCW.png)

## Built-in tasks v.s. custom tasks

In this demo, I will use a custom extension to provide the Temporary Access Pass directly to the user. There is, however, a [built-in task](https://learn.microsoft.com/en-us/azure/active-directory/governance/lifecycle-workflow-tasks#generate-temporary-access-pass-and-send-via-email-to-users-manager) that might be sufficient for most organizations. Note that this does not require any Logic App or delegation of permissions and has some additional security checks in it. The default task will send the TAP to the manager instead of the new hire, which might be good enough for most organizations.

The default task allows you to do some customization as well so that works really intuitively.

![](/assets/images/image-21.png)

My advice would be to start out with the templates and built-in tasks first.

## Requirements

This scenario has some important requirements. To start off, we need an Azure AD Premium P2 license because Lifecycle Workflows is part of Azure AD Identity Governance. We also need [access](https://learn.microsoft.com/en-us/azure/active-directory/governance/lifecycle-workflow-extensibility#prerequisite-logic-app-roles-required-for-integration-with-the-custom-task-extension) to an Azure subscription to create a new Logic App.

Next, we need a couple of attributes in place:

![](/assets/images/msedge_FcwapveUqO.png)

1. **userPrincipalName**. This will be part of the welcome email.
2. **mobilePhone**. This is where we will send the new Temporary Access Pass.
3. **EmployeeHireDate**. This is needed to schedule the workflow.
4. **otherMails**. This is the private email of the new hire. This is used to send the welcome email.\*\*\*

_\*\*\*You might want to use an extension attribute for this field in a hybrid scenario, as the otherMails attribute can not be synced with Azure AD Connect Sync._

Next, we need the manager attribute to be filled in. Some tasks will rely on the manager's property, at least in this case, it does.

![](/assets/images/image.png)

For this demo, I prepped Debra's account with the required attributes. Note that this account is a **cloud-only** account. If you want to test this on a hybrid account, please make sure you have synced the **_employeeHireDate_** with Azure AD Connect Sync or Cloud Sync.

To use Temporary Access Pass, it must be enabled on your Azure AD tenant. To do this, sign in to the Azure portal as an Authentication administrator and select Azure Active Directory > Security > Authentication methods > Temporary Access Pass to enable the policy for all or selected users.

## Create new Custom extension (Logic App)

When you use the wizard to create a new custom extension (recommended), the following items will be created for you automatically:

1. The Logic App itself.
2. The Trigger and Callback action.
3. System-managed identity.
4. Authorization policy.

If you want to use existing Logic Apps, please read more here: [Configure a Logic App for Lifecycle Workflow use - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/configure-logic-app-lifecycle-workflows)

In the Azure portal, head over to **_Identity Governance -> Lifecycle Workflows -> Custom extensions_** to create a new one.

![](/assets/images/image-3.png)

Configure the new extension to the following settings, and select your Azure subscription to create the new Logic App. I picked the Launch and wait option, to also get familiar with the callback option. You can also pick the Launch and continue option, but that will consider the task done when the Logic App is launched.

![](/assets/images/image-2.png)

After the wizard is done, you can check the new Logic App, and its settings. Note that it will only have two steps, the trigger, and callback URI. The wizard also created a new system-managed identity and configured the authorization policy.

![](/assets/images/image-4.png)

## Adding permissions to the system-managed identity

Now that our Logic App is created let's focus on the permissions. Since the Logic App needs to create new Temporary Access Passes (amongst other tasks), we will add the **_Authentication Administrator_** role to the managed identity to grant these permissions. This role will have enough permissions to get information about the subject and create a new Temporary Access Pass. We could also grant specific Graph API permissions like **_UserAuthenticationMethod.ReadWrite.All_**, but adding the role is less privileged for this use case.

First, grab the object ID from the managed identity. You need this in the next step.

![](/assets/images/image-7.png)

Then, find the app in Enterprise Applications, and add the Authentication Administrator role to the service principal (system managed identity).

![](/assets/images/image-6.png)

## Building the Logic App

Now that we have our Logic App created and have the permissions in place, we can start building our solution in Logic Apps designer. I won't go into detail here, but you can find the expanded capture [here](https://janbakker.tech/wp-content/uploads/2022/10/CvqzSgrjT7.png), or download the template from [GitHub](https://github.com/BakkerJan/LogicApps/blob/main/ExportedTemplate-Demo.zip).

![](/assets/images/image-9.png)

1. Setting the variable **_Audience_** to https://graph.microsoft.com so I can re-use it in the HTTP actions.
2. I use the ID to grab a few more attributes from the subject (new hire) to use have attributes like givenName and mobilePhone.
3. Grabbing the private email addresses from Graph API.
4. Since step 4 gives me an array, I use this step to grab the first item of the list.
5. Creating a new Temporary Access Pass using Graph API.
6. Send a text using the Twilio connector. Feel free to use any other connector, or sign up for a free trial.
7. Send an email to the private email of the new hire, and add the manager in cc.

## Building the workflow

Now that we have all the pieces in place, we can create the workflow. The easiest way is to start with one of the templates. In the Azure portal, head over to **_Identity Governance -> Lifecycle Workflows ->_** **_Workflows_**, and select **_\+ Create_** workflow to get started.

![](/assets/images/image-10.png)

Select the first template: _Onboard pre-hire employee_.

![](/assets/images/msedge_gNPjFS9C9q.png)

You can leave the displayname and description as is, or give it a custom name. Adjust the trigger details if you want, or go with the default of 7 days. We will not enable the schedule, and only run the flow on demand, so it does not matter for now.

![](/assets/images/image-11.png)

Next, leave the scope details as is, or adjust the expression to your needs. For now, we leave this to run for new hires in the Marketing department only.

![](/assets/images/image-12.png)

On the workflow task tab, delete or disable the default task, and add a new one.

![](/assets/images/image-13.png)

![](/assets/images/image-14.png)

After the task is added, click on the "_Run a Custom Task Extension_", next to the amber icon, and select the custom extension that we created earlier.

![](/assets/images/image-15.png)

At the final page of the wizard, review your settings and hit "**_Create_**" to build the workflow.

![](/assets/images/image-16.png)

## End results

When the workflow is created, select the new workflow, and click "**_Run on demand_**" on the overview page.

![](/assets/images/image-17.png)

Select your prepped testuser, and click "**Run workflow**"

![](/assets/images/image-18.png)

You can review the status in both the Workflow history, and the Logic App runs history.

![](/assets/images/image-19.png)

![](/assets/images/image-20.png)

If all goes well, Debra will receive both the email and SMS with the information about her new Temporary Access Pass.

![](/assets/images/image-8.png)

## Let's wrap up

Lifecycle Workflows in Azure AD Identity Governance are definitely a great asset to Azure AD. Administrators can now automate most common tasks that are often done manually for new hires, movers, or leavers. In this demo, we discussed the custom extensions, but I want to point out that the built-in tasks are also a great starting point for most processes. Think about adding users to groups or teams.

Please check out this documentation about the attributes needed for Lifecycle Workflows: [How to synchronize attributes for Lifecycle workflows - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/how-to-lifecycle-workflow-sync-attributes)

Stay tuned for more posts about this topic.

Learn more:  
[Plan a Lifecycle Workflow deployment - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/lifecycle-workflows-deployment)  
[Configure a Logic App for Lifecycle Workflow use - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/configure-logic-app-lifecycle-workflows)  
[Lifecycle workflows FAQs - Azure AD (preview) - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/workflows-faqs)

Stay safe!
