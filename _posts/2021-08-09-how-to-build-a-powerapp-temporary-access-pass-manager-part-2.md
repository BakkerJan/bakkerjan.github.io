---
title: "How to build a PowerApp - Temporary Access Pass Manager - Part 2"
date: 2021-08-09
categories: 
  - "entra"
  - "power-platform"
  - "security"
image: "/assets/images/1628535438.png"
---

## Part 2: App registration and Graph API permissions

This part of the series is around laying the base for your custom connector that we are going to build later on. Before we can do that, we need to create an app registration in Azure Active Directory. This app registration will hold the permissions and the secret.

Before creating an app registration, let's focus on the permissions. How do we know what permission we need? The quickest way to find that out is by using the [Microsoft documentation](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0) around the Graph API. The documentation is split into V1.0 and BETA. You can easily switch between the two sections.

![](/assets/images/image-1.png)

In our app, we are going to focus on a new feature in Azure AD, the Temporary Access Pass. The documentation is based on the **beta** section of the documentation. In nerd terms, this is where you will find the good stuff. Bookmark [this page](https://docs.microsoft.com/en-us/graph/api/resources/temporaryaccesspassauthenticationmethod?view=graph-rest-beta), we are going to need this a lot while creating our solution. Permissions can be delegated or application permissions, depending on your use case. You can read more about the different types of permissions [here](https://docs.microsoft.com/en-us/graph/auth/auth-concepts#delegated-and-application-permissions). When working with authentication methods, I recommend using delegated permissions.

![](/assets/images/image-2.png)

As we can tell from the documentation, we need the **_UserAuthenticationMethod.ReadWrite.All_** permissions to manage the Temporary Access Pass for other users. Because we are using delegated permissions, the user performing the action also needs one of the admin roles mentioned at the bottom of the page.

## Azure AD app registration

So let's create our app registration. Go to the [Azure AD portal](https://portal.azure.com) -> Azure Active Directory -> App registrations. Choose **_\+ New registration_**.

![](/assets/images/image-3.png)

In the next screen, pick a name for the registration. I recommend making this recognizable so that you and other admins know what it is used for. In the supported account types section, pick the top one setting (Single-tenant) For the redirect URL, enter **_https://global.consent.azure-apim.net/redirect_**

Click **_register_** to create the app registration.

![](/assets/images/image-4.png)

Next, take note of the application ID of the created app registration. We will need this later on.

![](/assets/images/image-80.png)

## Permissions

Next, we are adding the permissions for the Graph API. Select API permissions and click **_\+ Add a permission_**.

![](/assets/images/image-5.png)

Pick the Microsoft Graph API from the wizard.

![](/assets/images/image-6.png)

Next, choose **_delegated_** permissions and search for **_UserAuthenticationMethod.ReadWrite.All_**. Click Add permissions to add the selected permissions.

![](/assets/images/image-7.png)

After you added the permissions, you have to make sure that the permissions are granted for all accounts in the tenant. Click 'Grant admin consent for (tenant)' and select **Yes**.

![](/assets/images/image-9.png)

## Secret

Now that we granted the permissions for our app registration, we need to create a secret that we can use for authentication. Add the new secret, give it a proper name, and choose the expiration date.

![](/assets/images/image-11.png)

After the secret is created, take note of the secret, next to the application ID. This secret is shown only once, so if you forget to capture it, you'll have to create a new secret.

![](/assets/images/image-81.png)

We are now done with the preparations in Azure Active Directory. What have we built so far?

- App registration in Azure Active Directory
- Permissions to manage all authentication methods for all users in the tenant using Graph API
- Authentication in the form of a secret. We've also captured the application ID, tenant ID and secret.

Ready for the next part? Let's learn all about the Graph API and Graph Explorer! [Part 3: Graph API and Graph Explorer](https://janbakker.tech/how-to-build-a-powerapp-temporary-access-pass-manager-part-3/)
