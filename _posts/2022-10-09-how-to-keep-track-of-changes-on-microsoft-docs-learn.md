---
title: "How to keep track of changes on Microsoft Docs & Learn?"
date: 2022-10-09
categories: 
  - "entra"
  - "logic-apps"
image: "/assets/images/image-29.png"
---

When working with cloud services like Microsoft 365 or Azure Active Directory in particular, it's very important to stay on top of new features and/or product changes. As you might know, the documentation for these services is stored on GitHub. This is where those changes will often reflect.

I was inspired by the post of Albert-Jan Schot ([Get notified for PnP updates from GitHub · CloudAppie)](https://www.cloudappie.nl/notified-pnp-updates-github/), where he explained how to use the GitHub REST API to keep track of issues and new releases. That inspired me to build a Logic App that will keep me informed on changes in the articles that I'm interested in. Now, I'm fully aware of the capabilities of the platform itself to get notified on specific events, but if you don't hang out on GitHub all day (like me), this might be the solution for you.

In this post, I use Logic Apps, but this will also work with Power Automate. It's up to you!

I'd like to show you how you can build an automated flow that will notify you of commits on Azure AD documentation on a daily basis. We will use the free and open API from Github, so there is no need for authentication whatsoever. Do know that the API is limited, but if you trigger your Logic App or flow once a day, you should be fine.

My Logic App will run each day at 8 AM and will pull all commits for specific pages. Please reach out to Albert-Jan's post to learn how those URL's are built up.

In my example, we are looking at this URL:

```
https://api.github.com/repos/MicrosoftDocs/azure-docs/commits?path=/articles/active-directory
```

Note that this is our base URL. I will also specify some subpages that I'm interested in. In this case, MicrosoftDocs/azure-docs is our repo. All pages that follow are specified in the _path_ parameter.

![](/assets/images/image-24.png)

1. Repo
2. Path
3. Subpages

The commits are then filtered only to show the commits from the past 24 hours. Next, I will put all data into an HTML table that I can then send via email. Let's dive in!

## The Logic App / Power Automate flow

This Logic App is publicly available [here](https://github.com/BakkerJan/LogicApps/blob/main/NotifyOnDocsCommits.zip) and can be used as an example to build your own solution. I will now go through some details so that you understand what's going on.

![](/assets/images/image-22.png)

1. This will set the trigger of the Logic App. In this case, the flow will run every day at 8 AM.
2. This is the prefix to point to the GitHub API.
3. Here I point to the repo, which is MicrosoftDocs/azure-docs
4. The last part is the path. This will point to a deeper page within the repo.

![](/assets/images/image-23.png)

1. Here, I will initialize the HTML tables variable. This will store a bunch of HTML tables that will be sent via email later on.
2. These are the subpages that I want to monitor. You can simply add or remove pages to that array. Check this [page](https://github.com/MicrosoftDocs/azure-docs/tree/main/articles/active-directory) to see all the subpages for this path.
3. Here I will parse the pages, so I can use them later on as dynamic content.
4. Here, I created some CSS styling so that the HTML table will look pretty. Source: [Power Automate HTML Table Styling – Ryan Maclean (ryanmaclean365.com)](https://ryanmaclean365.com/2020/01/29/power-automate-html-table-styling/)

![](/assets/images/image-25.png)

1. Here I will do a for-each action. I use the body of the parsed subpages as the input.
2. Here I will get the commits from GitHub API using the variables that I created earlier.
3. This step will parse all info. I made some tweaks to the schema because some committers do not provide all attributes (shame). Some of the objects and strings do have null values.
4. Here I will filter the commits to show only commits within the timeframe specified in Expression.
5. The expression is currently set to -1 day so that it will grab all the commits from the last day.

![](/assets/images/image-26.png)

1. This step will create a nice HTML table and add the fields I'm interested in.
2. I will check if the body contains data. If not, no action will be taken. This will prevent empty HTML tables in the email later on.
3. If the table has data, I will add the HTML table to the variable.

I use a trick to properly format the URL. I learned that from this post: [How to add hyperlink in the 'Create HTML table' Power Automate action (tomriha.com)](https://tomriha.com/how-to-add-hyperlink-in-the-create-html-table-power-automate-action/)

![](/assets/images/image-27.png)

1. Next, I will check if the variable is not empty. If empty, no mail will be sent.
2. In this step, I will use the Outlook connector to send an email.
3. Here I have put the CSS styling and the HTML variable.
4. Last but not least, provide the email address.

## The end result

Okay, I must admit, it does not look pretty, but I really love the way this works. Now, I can check out all the commits from one single place. Right off the bat, I see a commit from Rob de Jong on the hybrid section, so that might tell us there is a new release of Azure AD Connect! See how effective this is?

![](/assets/images/image-28.png)

The deep link will take you straight to the commit to check out what changed. This can be really valuable information.

![](/assets/images/msedge_ceJMCgO0O6.png)

Not all commits do have a revealing message attribute that will tell you exactly what changed, but hopefully, that will improve in the future. So dear Microsoft committer, if you are reading this........ 😊

Stay safe!
