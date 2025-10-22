---
title: "Enrich Microsoft 365 profile card with extensions and custom attributes"
date: 2021-01-08
categories: 
  - "entra"
coverImage: "pexels-cottonbro-5077055-1.jpg"
---

Microsoft 365 is equipped with a very nice, but underestimated feature: [Profile cards](https://support.microsoft.com/en-us/office/profile-cards-in-microsoft-365-e80f931f-5fc4-4a59-ba6e-c1e35a85b501). I'm sure you know Microsoft Delve, and how it can enrich your Office 365 profile with relevant information such as hobbies, working hours, skills, and your birthday. Next to that, you can find information about recent projects, and the people that you worked with.

All this information and insights are getting more and more integrated with the profile card feature. You can find the profile card in almost any web version of Office 365.

![](/assets/images/image-4.png)

## Make additional attributes visible

Next to custom attributes, there are a few additional attributes that you can enable on the profile card, such as:

- UserPrincipalName
- Fax
- StreetAddress
- PostalCode
- StateOrProvince
- Alias

When enabled, these attributes are showing up in the profile card. You don't need any translation, because localization is built in. I will show you how to enable these later in the post. In this example, the card is expanded with the Alias and UserPrincipalName:

![](/assets/images/image-5.png)

## Custom attributes

You can also enrich your profile cards with additional attributes that are normally not visible to your users. You can pick any of the 15 [ExtensionAttributes or onPremisesExtensionAttributes](https://docs.microsoft.com/en-us/graph/api/resources/onpremisesextensionattributes?view=graph-rest-beta) (in case of [hybrid](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions)), and add them to your profile card. These extension attributes are also known as Exchange custom attributes 1-15.

In this example I added 4 extra attributes to the card:

- Cost Center
- Employment
- Employee number
- Date of entry

There are a couple of ways to see these attributes, for example by using the Graph Explorer.

```
GET https://graph.microsoft.com/beta/users/{UserPrincipalName}
```

![](/assets/images/image-7.png)

They are also visible in the the Exchange Admin Center.

![](/assets/images/image-6.png)

## How it's done

So, how do we add these attributes? In this example, I use [Graph Explorer](https://aka.ms/ge) to extend the profile card. It is an easy process. But first things first:

- in order to do this, you need to be a Global Administrator
- you can add only one profileCardProperty resource at a time
- it can take up to 24 hours before the additional fields show up on the card (mine took about 8 hours at least)

#### How to add additional attributes

Go to [Graph Explorer](https://aka.ms/ge) and sign-in with your Global Administrator account. Use the following API request to add the UserPrincipalName to the profile card. Make sure you replace the tenantID value. [How to find your tenantID.](https://docs.microsoft.com/en-us/onedrive/find-your-office-365-tenant-id)

```
POST https://graph.microsoft.com/beta/organization/{tenantid}/settings/profileCardProperties
```

```
{
  "directoryPropertyName": "UserPrincipalName"
}
```

![](/assets/images/2140-07-01-2021.png)

You can use _UserPrincipalName_, _Fax_, _StreetAddress_, _PostalCode_, _StateOrProvince_, and _Alias_.

_Note: The additional properties field label will automatically be shown in the language settings that the user has specified for Microsoft 365._

#### How to add extension attributes

Adding extension attributes are a bit more work because you also have to take care of the translation. Each attribute label can have localized values, however, this is not mandatory.

![](/assets/images/2133-07-01-2021.png)

```
POST https://graph.microsoft.com/beta/organization/{tenantid}/settings/profileCardProperties
```

In this example, I also added the Dutch translation for Cost center, which is "Kostenplaats". The language tag is "nl". I found [this post](https://www.codetwo.com/admins-blog/list-of-office-365-language-id/) where you can find your language tag.

```
{
    "directoryPropertyName": "customAttribute1",
    "annotations": [
        {
            "displayName": "Cost center",
            "localizations": [
                {
                    "languageTag": "nl",
                    "displayName": "Kostenplaats"
                }
            ]
        }
    ]
}
```

In case you don't want to add localization, the body will look like this:

```
{
    "directoryPropertyName": "customAttribute1",
    "annotations": [
        {
            "displayName": "Cost center",
            "localizations": []
        }
    ]
}
```

The attributes can be replaced with the following values:

extensionAttribute1 = _customAttribute1_  
extensionAttribute2 = _customAttribute2_  
......  
......  
......  
extensionAttribute15 = _customAttribute15_

Source: [Customize the profile card using the profile API in Microsoft Graph (preview) - Microsoft Graph | Microsoft Docs](https://docs.microsoft.com/en-us/graph/add-properties-profilecard#adding-custom-attributes)  

To see the current configuration of your profile card, simply run:

```
GET https://graph.microsoft.com/beta/organization/{tenantid}/settings/profileCardProperties
```

My card looks like this:

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#organization('39a5568b-aaxd-xxa-a6a5-xxxxxxxxx')/settings/profileCardProperties",
    "value": [
        {
            "directoryPropertyName": "customAttribute1",
            "annotations": [
                {
                    "displayName": "Cost center",
                    "localizations": [
                        {
                            "languageTag": "nl",
                            "displayName": "Kostenplaats"
                        }
                    ]
                }
            ]
        },
        {
            "directoryPropertyName": "UserPrincipalName",
            "annotations": []
        },
        {
            "directoryPropertyName": "Alias",
            "annotations": []
        },
        {
            "directoryPropertyName": "customAttribute2",
            "annotations": [
                {
                    "displayName": "Employment",
                    "localizations": [
                        {
                            "languageTag": "nl",
                            "displayName": "Dienstverband"
                        }
                    ]
                }
            ]
        },
        {
            "directoryPropertyName": "customAttribute3",
            "annotations": [
                {
                    "displayName": "Staff number",
                    "localizations": [
                        {
                            "languageTag": "nl",
                            "displayName": "Personeelsnummer"
                        }
                    ]
                }
            ]
        },
        {
            "directoryPropertyName": "customAttribute4",
            "annotations": [
                {
                    "displayName": "Date of entry",
                    "localizations": [
                        {
                            "languageTag": "nl",
                            "displayName": "Datum in dienst"
                        }
                    ]
                }
            ]
        }
    ]
}
```

After a long 24 hours of waiting, this is what the card looks like:

![](/assets/images/image-11.png)

After changing my Office 365 language to Dutch, the fields are translated. When the users' language is not available, the default language (en-us) is shown.

![](/assets/images/image-10.png)

## Good to know

As stated before, you can only add one attribute at a time. Updating or deleting single attributes is not possible at the moment. When you run either _PATCH_ or _DELETE_, the whole card is replaced or deleted. So make sure you got a backup of your profile card, and if you need to update or delete, add the "new" items one by one.

## Wrap things up

As you can see, this can add a lot of value to your organization. You might already use these attributes, so it's good to know that you can show them here as well. Not all attributes are meant for that purpose, so it's best not to expose the 'salary extension attribute'. Kidding of course! Just want to check if you read this article to the very end. If you made it until here, I'm sure you liked it ;) I hope this will give you some ideas, and help you further in your cloud journey.

More information can be found here:

[Customize the profile card using the profile API in Microsoft Graph (preview) - Microsoft Graph | Microsoft Docs](https://docs.microsoft.com/en-us/graph/add-properties-profilecard#adding-custom-attributes)  
[Customize the profile card in Win32 apps using registry keys - Office Support (microsoft.com)](https://support.microsoft.com/en-us/office/customize-the-profile-card-in-win32-apps-using-registry-keys-449afd21-6e5e-4b1f-8051-6515630d7537)

Stay safe!
