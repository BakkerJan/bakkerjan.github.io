---
title: "Azure MFA authentication method analysis. Share the results with Power Automate!"
date: 2020-09-08
categories: 
  - "entra"
  - "power-platform"
  - "security"
tags: 
  - "azure-mfa"
  - "flow"
  - "mfa"
  - "powershell"
  - "results"
coverImage: "image-15.png"
---

You might have seen the [sample script](https://docs.microsoft.com/en-us/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/), created by the Microsoft community, to run some analysis on your Azure MFA authentication methods. This script can be used to make recommendations on how to improve each user's MFA configuration.

You can run the script against your tenant, and the results can be exported to a CSV file. Wouldn't it be cool to share those results with your users straight away? With the use of Power Automate (Flow), we can easily send recommendations to each of our end users. Today I'll show you how this can be done in a few clicks.

## Run the script

First, we have to run the script. This is pretty straight forward. Here's an example:

```
.\MfaAuthMethodAnalysis.ps1 -TenantId 9959f32b-837b-41db-b6e5-32277e344292 -CsvOutput -Verbose
```

You can also target a specific group, if needed.

```
.\MfaAuthMethodAnalysis.ps1 -TenantId 9959f32b-837b-41db-b6e5-32277e344292 -TargetGroup 6424cd24-ee16-472f-bad6-85427c9febc2
```

Ensure you run the script with an account that can enumerate user properties. For least privilege use the **_User Administrator role_**.

## Prepare the CSV file

The CSV file is a comma-separated file. To keeps things simple, use Excel to convert these records to columns and turn them into a table manually.

![](/assets/images/image-11.png)

![](/assets/images/image-12-1024x83.png)

![](/assets/images/image-10.png)

If needed, do a clean-up on empty records or filter out the records/columns that you don't need. Save the file as an .XLSX file on SharePoint or Onedrive.

## Make the flow

Start off by creating a new flow with a manual trigger. Give it a proper name.

The flow in this example is really simple, but you can expand the flow to your own needs. To start, we need to pick up the Excel file and get all the rows from the table.

![](/assets/images/image-13.png)

Next, we use the Azure AD connector to get the properties of our users. We need this later on. Use the UserPrincipalName from the Excel file as your input value.

The next steps are optional if you don't want to differentiate your recommendations. I want to send a separate mail to users that have **_0_** MFA methods registered, so I compose the **_MfaAuthMethodCount_** to a float value. I used the float(outputs('ComposeMFAMethods')) as my expression.

![](/assets/images/image-14.png)

In my next step, I add a condition to split the flow. One for the non registered users, and one for the users that have 1 or more methods registered. In the last step, use the dynamic values from the previous steps to compose your email.

![](/assets/images/image-15.png)

Test and run the flow. The user will end up with the following email:

![](/assets/images/image-16.png)

## Wrap things up

As you can see, you can be creative here and make your own flow to send out these messages to your users. You could also use the Teams connector, and just send the recommendations by chat for example.

The script is open-source, so feel free to contribute! [https://docs.microsoft.com/en-us/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis](https://docs.microsoft.com/en-us/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis)/

Get the sample Flow from my [Github page](https://github.com/BakkerJan/Power-Automate/tree/master/MFA%20authentication%20method%20analysis%20Flow)
