---
title: "How to build a PowerApp - Temporary Access Pass Manager – Part 3"
date: 2021-08-09
categories: 
  - "entra"
  - "power-platform"
  - "security"
coverImage: "1628535438.png"
---

## Part 3: Graph API and Graph Explorer

If you are new to Graph API, Graph Explorer is a great tool to learn the first steps into the Microsoft Graph. Next to that, it will also help you to shape your custom connector in the next part. Before diving into that, we'll need to understand how Graph API works, and how Graph Explorer can help you with that.

First, let's bookmark the Graph Explorer for future use. Graph Explorer comes with the best short URL ever: [https://aka.ms/ge](https://aka.ms/ge). That's easy, right?

When you access Graph Explorer the first time, you are not signed in. That means that you can run queries against sample data. A nice way to explore the different types of queries and responses, without touching actual company data.

![](/assets/images/image-14.png)

When you sign in, you are prompted for consent.

![](/assets/images/image-15.png)

## Permissions

Just like we did with the app registration, your user account needs to have certain permissions to run queries. If you are running a query that needs extra permissions, Graph Explorer will tell you what permissions you have to consent to.

![](/assets/images/image-16.png)

You can also see an overview of all permissions using this menu option:

![](/assets/images/image-17.png)

Before moving into the next section, make sure that you consented to both UserAuthenticationMethod.ReadWrite and UserAuthenticationMethod.ReadWrite.All.

![](/assets/images/image-20.png)

## Temporary Access Pass

Now, let's see if we can focus on our theme and run some queries around the Temporary Access Pass section.

First, let's make sure that you can use this feature in Azure Active Directory. Make sure that the policy is enabled for your test users or all users. You can find these settings in [https://portal.azure.com](https://portal.azure.com) -> **_Azure Active Directory -> Security -> Authentication methods._**

![](/assets/images/image-19.png)

Next, we need documentation about the different types of queries. You can easily hop to the API references from Graph Explorer or just use [this link](https://docs.microsoft.com/en-us/graph/api/resources/temporaryaccesspassauthenticationmethod?view=graph-rest-beta).

![](/assets/images/image-18.png)

Here we can find all the possible queries like List, Create, Get and Delete. Also, every section holds a sample that we use to make our own queries.

![](/assets/images/image-23.png)

Let's run our first query and create a Temporary Access Pass for Adele.

![](/assets/images/image-24.png)

Use **POST** (1) as the method and make sure that you've selected **beta** (2) as the API version. Use the URL below, and replace AdeleV@M365x583104.OnMicrosoft.com with the UserPrincipalName of your test user.

```
https://graph.microsoft.com/beta/users/AdeleV@M365x583104.OnMicrosoft.com
/authentication/temporaryAccessPassMethods
```

**!!** Make sure to enter two brackets {} in the request body. Since we use an empty payload (4), the Temporary Access Pass will be created using the default settings that you can configure in the Azure portal.

![](/assets/images/image-21.png)

If you want, you can also use the request body to create a divergent access pass. An example of that is showing in the API reference. To make things as easy as possible, we'll stick with an empty payload for now.

```
{
  "@odata.type": "#microsoft.graph.temporaryAccessPassAuthenticationMethod",
  "startDateTime": "2021-01-26T00:00:00.000Z",
  "lifetimeInMinutes": 60,
  "isUsableOnce": false
}
```

If your query was successful, you should be able to see the Temporary Access Pass using the Azure portal.

![](/assets/images/image-25.png)

In Graph Explorer, replace the POST for **GET** and remove the brackets in the body. When you run the query, you should now see the Temporary Access Pass that you've created for Adele in the previous step.

![](/assets/images/image-26.png)

Let's see if we can delete the Temporary Access Pass as well. Now it is getting a little more complex. Here we need the ID of the Temporary Access Pass first. You can grab that from the response in the previous step.

![](/assets/images/image-27.png)

Now, change the method to **DELETE**, and paste the ID of the Temporary Access Pass after the query. The request body should be empty. Your query should look like this: (replace the UPN and the ID)

```
https://graph.microsoft.com/beta/users/AdeleV@M365x583104.OnMicrosoft.com
/authentication/temporaryAccessPassMethods/84a6bd43-a41a-4e5e-857d-d5e3989ad651
```

Check the Azure Portal, or run the GET query to check if the Temporary Access Pass is deleted. You should also see the records showing up in the audit logs in the Azure portal.

![](/assets/images/image-28.png)

We have no tested three types of queries:

1. Create a Temporary Access Pass
2. List the Temporary Access Pass
3. Delete the Temporary Access Pass

**Note: you can have only 1 Temporary Access Pass per user.**

## Wrap things up

So why is this valuable? Using the Graph Explorer, you will get comfortable with queries and reading JSON responses. Using the variety of samples, you can explore all features easily. Also, we need this data in our next step: creating the custom connector. Here, we are going to use these queries and responses from the queries that we just practiced.

If you want to learn more about Graph API, check out this [awesome series on Graph Explorer](https://developer.microsoft.com/en-us/graph/blogs/msgraphmailbag-use-graph-explorer-like-a-professional/) as well.

Feeling comfortable? Move on to the next part! [Part 4: Build a custom connector based on the Graph API](https://janbakker.tech/how-to-build-a-powerapp-temporary-access-pass-manager-part-4/)
