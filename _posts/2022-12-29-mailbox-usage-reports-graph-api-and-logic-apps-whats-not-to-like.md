---
title: "Mailbox usage reports, Graph API, and Logic Apps. What's not to like?"
date: 2022-12-29
categories: 
  - "logic-apps"
image: "/assests/images/pexels-anna-nekrashevich-6801648-scaled.jpg"
---

Exchange Online does a pretty good job when it comes to alerting on mailbox storage. Exchange Online provides three kinds of notifications when a user's mailbox is nearing or at capacity:

- **Warning**: The user receives an email warning that the mailbox is approaching the maximum size limit. This warning is intended to encourage users to delete unwanted mail.

- **Prohibit Send**: The user receives a prohibit-send notification email when the mailbox size limit is reached. The user can't send new messages until enough email is deleted to bring the mailbox below the size limit.

- **Prohibit Send/Receive**: Exchange Online rejects any incoming mail when the mailbox size limit is reached and sends a non-delivery report (NDR) to the sender. The sender has the option to try resending the mail later. To receive messages again, the user must delete emails until the mailbox is below the size limit.

And still, we find ourselves getting helpdesk tickets about mailboxes that can no longer send or receive email. It's time to dive into the Exchange Storage reports. A good report can be downloaded from the Microsoft 365 admin center, but that is a manual action. What if we could download the report automatically, and use it to trigger some actions?

![](/assets/images/image-9.png)

Up until now, you could pull the report from Graph API in CSV format only. But last week, I was exploring the beta documentation and stumbled upon an option to download the content in JSON format. Here is API call:

```
GET https://graph.microsoft.com/beta/reports/getMailboxUsageDetail(period='D7')?$format=application/json
```

Now, this is what I've been waiting for! But when we dig into the output, it's slightly disappointing. Most of the values about usage and thresholds are in **_bytes_**. Yikes! Let's see how we can fix that.

![](/assets/images/image-8.png)

Good to know: the report data is concealed by default. If you want to change that:

1. Go to the [Microsoft 365 admin center](https://admin.microsoft.com/).

3. Go to **Settings** > **Org Settings** > **Services**.

5. Select **Reports**.

7. Clear **Display concealed user, group, and site names in all reports**, and then select **Save**.

![](/assets/images/image-19.png)

Also, keep in mind that the data in the report has a **_24-48 hours_** delay compared to the actual data.

## Logic Apps to the rescue

It's time for some automation! Let's use Logic Apps to grab the report from Graph API and then pull some magic on the data. The solution that we build today can:

- Send an automated report to admins

- Send alerts to users when the mailbox usage reaches a specific threshold (in %)

- Get data converted to more readable units (MB/GB/Percentage)

Here's an example of the alert email:

![](/assets/images/image-10.png)

In the email body, I point the user to [https://outlook.office.com/mail/options/general/storage](https://outlook.office.com/mail/options/general/storage)  
On this page, you'll find an overview of your mailbox storage, and it gives the oppertunity to clean up your Inbox, Sent and Deleted Items.  

![](/assets/images/image-33.png)

* * *

The admin report will look like this:

![](/assets/images/image-11.png)

Do know that the email and report are fully customizable, depending on your requirements.

The next part of the blog post will show the manual steps on how to build the Logic App from head to tail, but I've also made an export available on [GitHub](https://github.com/BakkerJan/LogicApps/blob/main/ExchangeOnlineReporter.zip), that can be easily imported. That part is described at the end of the post.

## Let's build!

Okay, now that you have seen the result, let's see what we need to build this. Since we use Logic Apps, we need an Azure subscription, to begin with. Our first step is to create a new consumption-based Logic App from the Azure portal:

![](/assets/images/image-12.png)

Next, we need to create a managed identity. This is needed to download the report from Graph API and also read the user profiles for data enrichment. Create a new system-assigned identity, and take note of the Object (principal) ID. We need that in our next step.

![](/assets/images/image-13.png)

Now, we need to grant permissions to our Managed Identity. This step is a bit tricky, but Jan Vidar Elven has a great post on that subject: [Add Graph Application Permissions to Managed Identity using Graph Explorer | GoToGuy Blog](https://gotoguy.blog/2022/03/15/add-graph-application-permissions-to-managed-identity-using-graph-explorer/)

  
For this project, we need both **_Reports.Read.All_** and **_User.Read.All_**. After adding the permissions, your Managed Identity looks like this:

![](/assets/images/image-14.png)

After this, we can start building our Logic App using the Logic Apps designer. The first section will hold a few variables that act as parameters.

![](/assets/images/image-15.png)

**Recurrence**: this will set the frequency. In this example, the Logic App runs every **_five_** days.

**SendIndividualAlertToEachMailbox**: When this is set to **_true_**, users will receive an email when the threshold is reached. When this is set to **_false_**, no email will be sent to the user's mailbox.

**SendReportToAdmin**: When this is set to **_true_**, a report will be sent at the end of the flow. When set to **_false_**, no report will be sent.

Note: use the expression feature to set the **true** or **false** value. A string value with "true" or "false" won't work.

![](/assets/images/image-32.png)

* * *

**AdminEmail**: this will hold the addresses for the admin report. This can hold more than one address, separated by a semicolon. (;)

**AlertThreshold**: this will set the threshold for the mailbox usage alert to users. This number will represent a percentage, so 75%. This will be calculated against the **_issueWarningQuotaInBytes_** property of the mailbox.

**Report**: this will initialize a variable that will hold the report data. No action is needed.

![](/assets/images/msedge_JciB2AQELr.png)

* * *

![](/assets/images/image-17.png)

In the next step, we pull the report from Graph API using the HTTP connector. We use the Managed Identity to authenticate.

```
GET https://graph.microsoft.com/beta/reports/getMailboxUsageDetail(period='D7')?$format=application/json
```

* * *

After we pulled the report from Graph API, we need to parse the data. Use the sample payload from the previous step to generate the schema. You can also find an example [here](https://learn.microsoft.com/en-us/graph/api/reportroot-getmailboxusagedetail?view=graph-rest-beta#response-2).

<figure>

![](/assets/images/image-28.png)

<figcaption>

To avoid errors on users that never signed in, I slightly changed the schema.

</figcaption>

</figure>

In the second step, we filter the results so that deleted mailboxes are not included in the reports.

![](/assets/images/image-18.png)

* * *

In our next step, we take the output from the previous filter and do a for-each condition so that we can calculate the data for the individual users in the report.

First, we convert the '_storageUsedInBytes'_ and _'issueWarningQuotaInBytes'_ values from integer to float so that we can calculate the percentage.

![](/assets/images/image-20.png)

```
float(items('For_each')?['storageUsedInBytes'])

float(items('For_each')?['issueWarningQuotaInBytes'])

mul(div(outputs('Compose_MailboxStorageUsedInBytes'),outputs('Compose_MailboxIssueWarningQuotaInBytes')),100)
```

The percentage is calculated against the _issueWarningQuotaInBytes_ value.

* * *

The next few steps are used to convert the most used values to MB or GB. This is done by dividing the values through 1073741824 (GB) and 1048576 (MB). Also, I use the formatNumber feature to prettify the outcome.

![](/assets/images/image-21.png)

```
formatNumber(div(outputs('Compose_MailboxStorageUsedInBytes'),1073741824),'F2')

formatNumber(div(outputs('Compose_MailboxStorageUsedInBytes'),1048576),'F0')

formatNumber(div(outputs('Compose_MailboxIssueWarningQuotaInBytes'),1073741824),'F0')

formatNumber(div(float(items('For_each')?['prohibitSendQuotaInBytes']),1073741824),'F0')

formatNumber(div(int(items('For_each')?['prohibitSendReceiveQuotaInBytes']),1073741824),'F0')
```

* * *

Next, I compose both Last Activity Date and the Report Refresh Date. For the Last Activity Date, I use an if statement to handle users that have never signed in.

![](/assets/images/image-22.png)

```
if(empty(items('For_each')?['lastActivityDate']),'Not found',formatDateTime(items('For_each')?['lastActivityDate'],'D'))

formatDateTime(items('For_each')?['reportRefreshDate'],'D')
```

* * *

To compose the Quota status, I use a complex nested if statement. Depending on the _storageUsedInBytes_ value, the user will get the status:

A. Send & Receive Prohibited  
B. Send Prohibited  
C. Warning Issued  
D. Good (under limits)

```
if(less(items('For_each')?['storageUsedInBytes'],items('For_each')?['prohibitSendReceiveQuotaInBytes']),if(less(items('For_each')?['storageUsedInBytes'],items('For_each')?['prohibitSendQuotaInBytes']),if(less(items('For_each')?['storageUsedInBytes'],items('For_each')?['issueWarningQuotaInBytes']),'D. Good (under limits)','C. Warning Issued'),'B. Send Prohibited'),'A. Send & Receive Prohibited')
```

Then, depending on the parameter _SendIndividualAlertToEachMailbox_, an email will be sent to the user if the threshold is reached. Before sending the email, it will pull the user info from Graph API to get the correct email address.

![](/assets/images/msedge_trG5rFU8Xy.png)

```
GET https://graph.microsoft.com/beta/users/@{items('For_each')?['userPrincipalName']}?$select=displayName,givenName,mail
```

* * *

Remember, this is the threshold in % that we set earlier using the _AlertThreshold_ variable.

![](/assets/images/image-25.png)

The last step in the for-each section is used to build the report. This will append an object to the array for each user. In the next step, we will use this data source to send create the report. I used numbering so that the headers would line up in the way I wanted them.

In the last step, I do another condition to read the _SendReportToAdmin_ parameter. If this is set to **_true_**, a report will be created and sent to the address(es) that you provided in the _AdminEmail_ parameter.

First, I do a quick sort on the array to make sure the mailboxes that need attention will end up at the top of the report.

```
sort(variables('Report'),'12. Quota Status')
```

![](/assets/images/image-26.png)

* * *

In the final step, an HTML table will be created based on the report array. Then I provide some custom CSS to prettify the table a little bit. Then, an email is sent with the CSS styling object and the HTML table.

```
<style>
table {
  border: 1px solid #1C6EA4;
  background-color: #EEEEEE;
  width: 100%;
  text-align: left;
  border-collapsea: collapse;
}
table td, table th {
  border: 1px solid #AAAAAA;
  padding: 3px 2px;
}
table tbody td {
  font-size: 13px;
}
table thead {
  background: #17576B;
  border-bottom: 2px solid #444444;
}
table thead th {
  font-size: 15px;
  font-weight: bold;
  color: #FFFFFF;
  border-left: 2px solid #D0E4F5;
}
table thead th:first-child {
  border-left: none;
}
</style>
```

![](/assets/images/image-27.png)

That's it for the Logic App. In the next part, I will show how to import the solution from Github.

## Import the Logic App from GitHub

Now, building this solution from scratch might take some time, especially when Logic Apps are not really your thing. First, download the source files from GitHub: [LogicApps/ExchangeOnlineReporter.zip at main · BakkerJan/LogicApps (github.com)](https://github.com/BakkerJan/LogicApps/blob/main/ExchangeOnlineReporter.zip)

Check if you see two files, one for the Office 365 connector, and one for the Logic App itself.

![](/assets/images/image-29.png)

First, we need to import the Office 365 connector. Go the the Azure portal, and search for **_Custom deployment_**.  
Load the **_Office365\_template.json_** file and click **Save**.

![](/assets/images/msedge_DfZKDDyfae.png)

Select an existing resource group, or create a new one. The default name for this connection is **office365**. You can leave it like that. Next, click **Review + create**.

![](/assets/images/msedge_X0f0ps4403.png)

The validation should pass. After that, click **Create**.

![](/assets/images/msedge_a4J1Ahnm3D.png)

When the deployment is competed, go to the newly created resource.

![](/assets/images/msedge_QTLWIshUyU.png)

Take note of the Resource ID and Subscription ID. We need this for the next step.

![](/assets/images/msedge_17JNXMvyqM.png)

Now we need to preperate the template for the Logic App. Since it need a reference to the Office 365 connector, we need to do a small change to the file before we can deploy it. Open the LogicApp\_template.json file (I use [Visual Studio Code](https://code.visualstudio.com/)), and replace the value of the **_connectionID_** with the value of the **_resourceID_**. Then, replace the **_subscriptionID_**.

Save the file!

![](/assets/images/Code_ToeWy8bKbX.png)

Now, repeat the process to import the template with the use of Azure Resource Manager. (like we did with the Office 365 connector)

![](/assets/images/msedge_LKwwu7tl0T.png)

![](/assets/images/msedge_QTLWIshUyU-1.png)

After the deployment is done, go to the new resource (Logic App) and find the connecton blade to update the Office 365 credentials.

![](/assets/images/msedge_mZaP6JkBPY.png)

Finally, we need to grant perissions to the new Managed Identity, like we discussed earlier. You can find the Object ID on the Identity blade. Now, grant both **_Reports.Read.All_** and **_User.Read.All_** using [Graph Explorer](https://aka.ms/ge).

Please reach out to this post for a step by step tutorial: [Add Graph Application Permissions to Managed Identity using Graph Explorer | GoToGuy Blog](https://gotoguy.blog/2022/03/15/add-graph-application-permissions-to-managed-identity-using-graph-explorer/)

![](/assets/images/msedge_M1vGALmDpL.png)

After this is completed, you can open the designer to configure and test the Logic App.

![](/assets/images/image-31.png)

## Let's wrap up

I hope this post was helpful to you. It shows how we can use the data from Graph API to build custom solutions. Initially, the idea for this post came from my good friend and co-worker [Sjoerd Zandstra](https://www.linkedin.com/in/szandstra/). After we discovered the Graph API endpoint that could spit out JSON, we started building. Along the way, many challenges came up, so it was a great learning experience for both of us.

If you have a good idea on how to improve this solution, or if you have feedback, please reach out. I could only test this with limited user data, so I'd love to see how this runs on bigger tenants.

Stay safe!
