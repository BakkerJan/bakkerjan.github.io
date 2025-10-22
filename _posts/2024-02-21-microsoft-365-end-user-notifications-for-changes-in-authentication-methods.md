---
title: "Microsoft 365 end-user notifications for changes in authentication methods"
date: 2024-02-21
categories: 
  - "entra"
  - "logic-apps"
  - "security"
coverImage: "pexels-mike-bird-191325-scaled.jpg"
---

When moving away from traditional and weak authentication methods like passwords to stronger ones like Authenticator App and passkeys, it's essential to keep informed when some of these methods change. Organizations moving to modern authentication are facing new challenges around onboarding and recovery of authentication methods, as attackers can also use this to settle in someone's account by simply adding an extra authentication method. Entra ID will log this event, but no out-of-the-box feature informs the user.

This step-by-step tutorial will build a solution leveraging Azure Event Hub and Logic Apps. These components will work together to inform the end-user of any change to the authentication methods so they can take action if that event turns out to be fraudulent.

![](/assets/images/Azure-Event-Hubs-Logic-Apps-2.png)

A quick note upfront. This solution is based on an [article from Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/tutorial-enable-security-notifications-for-audit-logs) and is using Azure Event Hubs. There are many ways to achieve the same result, but in this case, Azure Event Hubs is the solution that extends the Entra ID audit logs so we can easily trigger on specific events. This can suit both small and larger organizations as Azure Event Hubs is very scalable. In this example, I use the Basic tier with just 1 Throughput Unit. This supports up to 1 MB per second or 1000 events per second (whichever comes first). Depending on your needs, you can scale up by increasing the Throughput Units. From what I can see, the costs for this solution will be very reasonable, but that might depend on the size of your organization and the number of audit events in Entra ID.

However, your organization may already ingest Entra ID logs in a different store like Log Analytics or Sentinel. Ask yourself if you can integrate with that existing solution first before building a new solution. The approach in this post is only built for notifications and therefore not suitable for log retention. All events will be deleted after 1 hour (max 24-hour message retention)

Please read this article on storing Microsoft Entra Logs in Azure:

[Logging Into the Future: Smart Strategies for Storing Microsoft Entra Logs in Azure (natehutchinson.co.uk)](https://www.natehutchinson.co.uk/post/smart-strategies-for-storing-microsoft-entra-logs-in-azure)

![](/assets/images/image-42.png)

![](/assets/images/image-40.png)

This step-by-step tutorial is a great learning experience and touches many Azure services. Although most steps are explained in detail, there might still be stuff that does not work as expected at your end. For your convenience, I've also exported the Logic App to [Github](https://github.com/BakkerJan/LogicApps/blob/main/EntraIDAlerts.zip) so that you can deploy it into your Azure subscription as a reference.

[![](/assets/images/Octicons-mark-github.svg-1024x1024.png)](https://github.com/BakkerJan/LogicApps/blob/main/EntraIDAlerts.zip)

## What do we need?

For this solution, we need a couple of things:

- An Azure subscription

- An Event Hubs Namespace

- An Azure Event Hub

- A Logic App

- A managed identity (go passwordless!)

- A (shared) mailbox

## Event Hubs Namespace and Event Hub

Let's start with creating the Event Hubs Namespace and the Event Hub. In the Azure portal, create a new Event Hubs Namespace. For the purpose of this demo, I picked the basic tier and 1 Throughput Unit and let all the properties default. Since we picked the basic tier, the namespace needs to be public. Private access and IP firewall rules are only available on Premium or Standard namespaces.

![](/assets/images/image-43.png)

Next, we create a new Azure Event Hub and set the retention to **1 hour**.

![](/assets/images/image-44.png)

## Data ingestion from Entra ID

Now that we have created our Event Hub, we need to feed the audit data from Entra ID. From the Microsoft Entra admin center, go to Identity -> Monitoring & Health -> Diagnostic settings, and configure the Audit logs to be sent to the Azure Event Hub.

![](/assets/images/image-57.png)

Now that we have our audit data flowing, let's move on with the next step.

## The Logic App

Now, we need a new Logic app for the automation part. Create a new, **consumption-based** Logic App.

![](/assets/images/image-46.png)

After creation, go to the new resource and create a new system-managed identity. We need this for authentication. Grab the Object (principal) ID. We need this in the next step.

![](/assets/images/image-51.png)

Now, the managed identity needs access to the Azure Event Hub. Using the Access Control (IAM) blade of your Event Hubs Namespace, add the **_Azure Event Hubs Data Receiver_** role to the managed identity.

![](/assets/images/image-54.png)

As this managed identity will do some Graph API calls later, I also assign the **Directory Reader role**.

![](/assets/images/image-65.png)

Next, start the Logic App editor and pick the blank template.

![](/assets/images/image-48.png)

Now, find the Event Hubs trigger: **_When events are available in Event Hub._**

![](/assets/images/image-55.png)

If this is the first connection to Event Hubs, you need to create a new connection. First, let's find the Namespace URL. This URL can be found on the **Overview** page of your Event Hubs Namespace. Copy the value.

![](/assets/images/image-56.png)

Give your connection a proper name, and select Logic Apps Managed Identity as your Authentication Type. Enter your namespace endpoint URL. Make sure you use the correct format: **_sb://<yournamespaceURL>_**

![](/assets/images/image-49.png)

After the connection is created, select your Event Hub name, and select **_application/json_** as the content type. You can also define the correct interval for your trigger.

![](/assets/images/image-50.png)

**Important!** Since this trigger will be flooded with audit events from Entra ID, we want to scope it down a little bit. As we are only interested in authentication method changes (for now) we use Trigger Conditions. From the Event Hub trigger, go to Settings.

![](/assets/images/image-58.png)

Now add this value as a conditional trigger:

```
@equals(triggerOutputs()?['body']?['ContentData']?['records'][0]['properties']?['loggedByService'],'Authentication Methods')
```

Or like this:

```
@equals(triggerOutputs()?['body/ContentData/records'][0]['properties/loggedByService'],'Authentication Methods')
```

![](/assets/images/image-59.png)

A big thanks to [Brian Timp](https://twitter.com/BrianTimp) for the help on this one 💪

Save the Logic App before going to the next step. Most of the work is done now.

* * *

A personal note before we continue: in the original post from Microsoft Learn, the Logic App is using Parse JSON to structure the data from the TriggerBody. This makes it really easy to select dynamic data.  
  
However, during testing, I saw inconsistent data and that every alert was built differently. This gave a lot of errors during parsing, so I decided to build this logic app without the parsing and manually select data using **expressions**. This takes a little more skill, but hopefully, you can use the examples from this post to make sense of the JSON data.

* * *

In the next step, we need a for each action, as the body may hold more than one record at a time. Use this value:

```
triggerBody()?['ContentData']?['records']
```

![](/assets/images/image-60.png)

Next, we want to do some more filtering so we can really focus on the events that matter to us. Here's the logic:

**_IF_**

items('For\_each')?\['operationName'\] contains registered security info _**OR**_  
items('For\_each')?\['operationName'\] contains deleted security info **_OR_**  
items('For\_each')?\['operationName'\] contains updated security info

**_AND_**  
  
items('For\_each')?\['properties'\]?\['result'\] is equal to success

![](/assets/images/image-61.png)

Use an expression to build your logic.

![](/assets/images/image-62.png)

Don't forget to group the conditions:

![](/assets/images/image-63.png)

Next, we compose body of the email.

![](/assets/images/image-64.png)

You can use this example or create one from scratch.

```xml
<div>
 <h2>
     You recently changed your authentication methods
 </h2>
 <p>
     We have been notified of the following action: (operation) on (date & time). <br><br>
     If you initiated this, no action is required. <br><br>
     If you haven't, please report it now. <br><br>
     <b>Instructions</b>
     <ol>
         <li>Review your account activity in <a href="https://mysignins.microsoft.com/security-info" class="link">Microsoft Security Info</a>.</li>
         <li>If you do not recognize this action, report it immediately:</li>
         <ul>
             <li>Go to <a href="#" class="link">ReportItNow</a> and select your security event.</li>
             <li>Provide any additional information in the form and submit.</li>
         </ul>
     </ol>
     <b>Information and Support</b>
     <ul>
         <li>Technical Assistance - Contact <a href="#" class="link">Helpdesk</a> support services</li>
     </ul>
     <b>Do NOT reply to this email. This is an unmonitored mailbox.</b><br>
     For more information, contact the <a href="#" class="link">Security Department</a>
     <br><br>
     <a href="#"><button type="button">Report device</button></a><br><br>
     <div class="footer">
         JanBakker.tech<br>
         <br>Facilitated by <br>
         <img src="https://janbakker.tech/wp-content/uploads/2020/02/JanBakkerTechLogo.png" alt="Company Logo" style="height:70px;">
     </div>
     <style>
         .link {
             text-decoration:none;
             color: #0078D4
         }
         button {
             background-color: #0078D4;
             color: white;
             padding: 10px;
             border-radius: 5px;
             text-decoration: none;

         }
         button:hover {
             cursor: pointer;
         }
         .footer {
             width: 100%;
             height: 10%;
             padding-top: 10px;
             padding-left: 10px;
             padding-right: 10px;
             background-color: rgb(237, 237, 237);
         }
     </style>
 </p>
</div>
```

To enrich your email, you can use dynamic data like these examples:

```
items('For_each')?['operationName']
formatDateTime(items('For_each')?['time'], 'g')
items('For_each')?['resultDescription']
items('For_each')?['properties']?['initiatedBy']?['user']?['userPrincipalName']
items('For_each')?['properties']?['initiatedBy']?['user']?['ipAddress']
```

In the next step, we are gathering some details about the affected user so that we can grab the email and manager properties (optional).

![](/assets/images/image-66.png)

Because we assigned the Directory Reader role to the managed identity earlier, we can now use it to do Graph API calls.

```
GET https://graph.microsoft.com/v1.0/users/items('For_each')?['properties']?['targetResources'][0]?['id']
```

We can use the same trick to get the users' manager. (optional)

![](/assets/images/image-67.png)

```
GET https://graph.microsoft.com/v1.0/users/items('For_each')?['properties']?['targetResources'][0]?['id']/manager
```

In the last step, we [send the mail using Graph API](https://learn.microsoft.com/en-us/graph/api/user-sendmail?view=graph-rest-1.0&tabs=http) and a shared mailbox. Permissions to the shared mailbox are granted using [RBAC policies for Exchange](https://janbakker.tech/a-love-story-about-role-based-access-control-for-applications-in-exchange-online-managed-identities-entra-id-admin-units-and-graph-api/).

![](/assets/images/image-71-1024x71.png)

![](/assets/images/image-68.png)

```
POST https://graph.microsoft.com/beta/users/alerting@M365x341716.onmicrosoft.com/sendMail
```

Here, I use these dynamic values as expressions:

outputs('ComposeEmailBody') as the email body.  
body('GetManager')?\['mail'\] as the ccRecipient  
body('GetUserInfo')?\['displayName'\] as part of the subject  
body('GetUserInfo')?\['mail'\] as the toRecipient

![](/assets/images/image-74.png)

**Note: Sending email using a managed identity requires a bit more preparation and is not included in this post. Please read [this post](https://janbakker.tech/a-love-story-about-role-based-access-control-for-applications-in-exchange-online-managed-identities-entra-id-admin-units-and-graph-api/) for a step-by-step tutorial. If you're not ready to send mail via Graph API, use the Outlook connector instead.**

![](/assets/images/image-69.png)

If you don't want to add the manager in cc, delete this part from your email body, and delete the 'GetManager' step.

![](/assets/images/image-73.png)

## The end result

The end-user will now be notified if the authentication methods are changed. I have tested these few scenarios, but you in theory all events in the Authentication Methods category may trigger the Logic App:

- Admin changed phone method for user

- Admin registered phone method for user

- Admin changed phone method for user

- Admin registered phone method for user

- Admin registered temporary access pass method for user

- Admin deleted temporary access pass method for user

- User deleted Mobile Phone Call and SMS

- User registered Mobile Phone SMS

- User deleted Authenticator App with Notification and Code

- User registered Authenticator App with Notification and Code

- User registered Fido

- User deleted Fido

Add or delete an authentication method to a test account, such as a Temporary Access Pass or mobile phone, to test this out.

![](/assets/images/image-72.png)

Check the audit logs for the event to appear.

![](/assets/images/image-77.png)

You can also check if the Logic App is triggered.

![](/assets/images/image-78.png)

Shortly after the change occurred, the user is notified with a shiny email. It usually takes up to 5- 6 minutes, depending on your trigger interval, of course.

![](/assets/images/image-75.png)

## Wrap up

I hope you learned something new today. Or perhaps you have improvements that you'd like to share! Please let me know in the comments or write me an email.

Do know that this format can be used for many more use cases. Do you have a nice use case in mind and need help? Let me know!

Stay safe!
