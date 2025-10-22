---
title: "Use Graph API data in Power BI using Logic Apps"
date: 2020-05-09
categories: 
  - "entra"
  - "logic-apps"
  - "power-bi"
  - "power-platform"
tags: 
  - "app-registration"
  - "azure-ad"
  - "azure-blob"
  - "blob-container"
  - "graph-api"
  - "http"
  - "logic-app"
  - "power-query"
  - "powerbi"
coverImage: "Graph-API-Logic-Apps-PowerBI-5.png"
---

Some things in the modern connected world seem so common that you just assume it's possible by nature. Getting your Microsoft Graph API data into Microsoft Power BI for example. That must be easy peasy right? Well....

When I start looking for ways to do this, I assumed there was a builtin connector available in Power BI that I could use. Guess what? There is not (yet). There is a connector for the **Microsoft Security Graph**, but that one "only" gives back the data from the security products. Just good to know that it's out there, but that's not what we're looking for.

When you look for solutions on the internet I read about all these methods that work for a while and then breaks. An example of that is the use of the OData feed connector in Power BI. This used to work for a while, but all of the sudden users could [not authenticate any longer](https://community.powerbi.com/t5/Issues/Error-getting-OData-from-Microsoft-Graph-Access-to-the-resource/idi-p/430087). Turn out, that OData is not supporting OAth authentication (anymore).

![](/assets/images/PBIDesktop_8pid0Vb9bI.png)

So, I found [this article](https://blog.yannickreekmans.be/connect-power-bi-to-microsoft-graph/) from Yannick that's a great workaround. It uses an Azure Function that acts as a proxy, so you have to re-write your queries to match your function URL. As this would work for sure, I was looking for an easier approach (no offense Yannick ;) )

The next option is a custom power Query Connector. There is a [good example of how to build this](https://docs.microsoft.com/en-us/power-query/samples/mygraph/readme), but when I saw the Github issues related to this, I saw that this method also broke.

![](/assets/images/image-25.png)

So I could not find any working solution for this. I discussed this with my colleagues and we came up with a way to get this done. I'm happy to share this with you guys (and girls). Enough with the chit-chat, on with the show!

#### Logic Apps to the rescue!

In this demo, I am going to show you how you can use Logic apps (or Power Automate if you prefer) to pull the data out of the Microsoft Graph API. Take a look at the overview of the solution.

1. Power Query in Power BI sends a POST HTTPS request to kick off the Logic App.
2. The Logic App uses an HTTP connector with Oath authentication to get the data out of Graph API.
3. The JSON data is sent back to Power BI.

![](/assets/images/Graph-API-Logic-Apps-PowerBI-5.png)

#### App registration

_Earlier, I wrote a [blog about Power Automate](https://janbakker.tech/2020/04/01/use-power-automate-for-your-custom-dynamic-groups/) where I showed all the steps to create an app registration. I don't go over all the steps here. You can use the previous blog as a reference if you're not familiar with app registrations, API permissions, and secrets._

We need an app registration in Azure AD to authenticate to Graph API. Create the app registration and add the right permissions. To see what permissions you'll need, head over to the [Graph API reference](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-beta). In this example we are getting the Sign-in logs as an example, so we'll need to add the **AuditLog.Read.All** and **Directory.Read.All** permissions. Don't forget to grant admin consent after you applied the permissions.

![](/assets/images/image-26.png)

When the app registration is created, create a **secret**. When done, write down the properties of the app registration for later use.

- Application (client) ID
- Directory (tenant) ID
- Secret

#### Logic App

Next, we are going to create the Logic App. Create a new Logic App and pick the trigger: **When a HTTP request is received**

![](/assets/images/image-27.png)

Use the following JSON body Schema:

```
{
    "properties": {},
    "type": "object"
}
```

When you save the trigger, the HTTPS POST URL will be created. We are going to use this later.

![](/assets/images/image-28.png)

Our next step in the Log App is the HTTP request to call the Graph API. For this example, I calling for the Sign-in Logs using: **https://graph.microsoft.com/beta/auditLogs/signIns**. For authentication, pick Active Directory OAuth and fill in the client ID, Tenant ID and secret from app registration.

![](/assets/images/image-74.png)

Don't forget to enable pagination if you request large amounts of data.

![](/assets/images/image-42.png)

Our final step is to create a response. Just pick the body content from the HTTP request.

![](/assets/images/image-30.png)

Your final Logic App will look like this:

![](/assets/images/image-31.png)

#### Power Query

Head over to Power BI Desktop to create our Power Query.

![](/assets/images/image-32.png)

Switch over to the _Advanced Editor_ and create the query.

```
let
    url = "https://your_logic_app_endpoint_post_url",
    body  = "{}",
    Parsed_JSON = Json.Document(body),
    BuildQueryString = Uri.BuildQueryString(Parsed_JSON),
    Source = Json.Document(Web.Contents(url,[Headers = [#"Content-Type"="application/json"], Content = Text.ToBinary(BuildQueryString) ] ))
in
    Source
```

Replace the URL with the URL from your Logic App trigger.

![](/assets/images/image-39.png)

When the query asks for credentials, just pick an anonymous connection.

![](/assets/images/image-40.png)

Next, we need to parse the data and convert it into a table.

![](/assets/images/image-33.png)

You can then expand the data to new rows and expand the columns. Depending on your data, multiple values need to be expanded.

![](/assets/images/image-34.png)

![](/assets/images/image-35.png)

You can expand all the columns and only select the data that you care about. The steps will look like this, depending on what data you are working with:

![](/assets/images/image-36.png)

You can now go on and take care of your dataset to use it in your Power BI reports. Rename the query to a recognizable phrase to keep the overview.

When done, click Close & Apply.

![](/assets/images/image-43.png)

You can now use the data to build your reports. I suck at Power BI, but I'm sure someone can build some nice reports with this data ;)

![](/assets/images/PBIDesktop_qYsFCrLB1N.png)

#### Wrap up

Using Graph API data in your Power BI reports can give some great insights. The sky is the limit here. I assume, since you stumbled upon this blog, you got your own use-case. To give you some ideas:

- You can pull all your [users](https://docs.microsoft.com/en-us/graph/api/user-get?view=graph-rest-beta&tabs=http) and [groups](https://docs.microsoft.com/en-us/graph/api/resources/group?view=graph-rest-beta) from Azure AD, with all extended attributes
- You can pull [usage reports](https://docs.microsoft.com/en-us/graph/api/resources/report?view=graph-rest-beta) to keep track of your adoption strategy
- List all [Teams](https://docs.microsoft.com/en-us/graph/teams-list-all-teams)
- List all your [Conditional Access policies](https://docs.microsoft.com/en-us/graph/api/conditionalaccessroot-list-policies?view=graph-rest-beta&tabs=http)

TIP: you can clone your Logic App. I won't recommend building more than one data request behind each trigger.

![](/assets/images/image-41.png)

#### I bet you can do this better!

_I'm sure there are more ways to do this. I'd like to hear back from you. Do you think you have something way better? Let me know!_

I've also found [this workaround](https://github.com/JoaoLucindo/DataConnectors/blob/master/samples/Planner%20Connector%20-%20Gateway%20Refresh%20Support/Planner/Readme.md). With this solution you'll have to install the Power BI Gateway, but auto refresh will work.

* * *

#### Update 10-05-2020 ( Azure Blob Storage)

You can also use an Azure Blob Storage Container (or basically any data source) to store the JSON data. This way you can handle much bigger chunks of data. Now, I am not going trough all the steps here, but to give you an idea of how this would work, I created this overview:

![](/assets/images/Graph-API-Logic-Apps-PowerBI-Azure-BLOB-Container.png)

Just create a new storage account in Azure and build your Logic App like this:

![](/assets/images/image-44.png)

It will drop the raw JSON data output of the HTTP request into a new blob. Whenever you re-run this Logic App, the blob is **_replaced_** by default. In Power BI, use the Azure Blob Storage connector to connect to your data source.

![](/assets/images/PBIDesktop_pKfa229Dsc.png)

Fill in the path (_minus the / at the end_).

![](/assets/images/image-46.png)

Next, enter the access key.

![](/assets/images/image-45.png)

Repeat the earlier steps to parse the JSON into tables.
