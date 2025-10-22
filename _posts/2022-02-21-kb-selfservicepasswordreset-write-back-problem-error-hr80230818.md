---
title: "KB - SelfServicePasswordReset write-back problem - error hr=80230818"
date: 2022-02-21
categories: 
  - "knowledgebase"
image: "/assests/images/image-18.png"
---

This is a knowledgebase item. Hope it helps you out someday.

Now, since you landed on this page, I assume you've got the following issue:

1. Azure AD SelfService Password Reset worked like a charm for quite some time.
2. All of the sudden it stopped working, and you have no idea why. You have checked the permissions on the service account, and all looks good.
3. You are in a hybrid setup, and use password write back. All checkmarks are green.
4. Azure AD audit logs contain OnPremisesAdminActionRequired or ADAdminActionRequired as failure.

Your users are prompted with this error when trying to do a password reset using Azure AD Self Service Password Reset Portal.

![](/assets/images/image-18.png)

On your ADConnect server, you will find this event:

```
Synchronization Engine returned an error hr=80230818, message=The management agent run was terminated because a domain controller could not be contacted.
```

Event ID: **33001**  
Source: **PasswordResetService**

![](/assets/images/1645438201-1024x117.png)

```
TrackingId: xxxxxxxxxxxxxxxxxxxxxxxxxx, Reason: Synchronization Engine returned an error hr=80230818, message=The management agent run was terminated because a domain controller could not be contacted., Context: cloudAnchor:xxxxxxxxxxxxxxxxxxxxxxxxxx, SourceAnchorValue: xxxxxxxxxxxxxxxxxxx, UserPrincipalName: xxxxxxxxxxxxxxxxxxxxxxxx, Details: Microsoft.CredentialManagement.OnPremisesPasswordReset.Shared.PasswordResetException: Synchronization Engine returned an error hr=80230818, message=The management agent run was terminated because a domain controller could not be contacted.
   at AADPasswordReset.SynchronizationEngineManagedHandle.ThrowSyncEngineError(Int32 hr)
   at AADPasswordReset.SynchronizationEngineManagedHandle.ChangePassword(String cloudAnchor, String sourceAnchor, String oldPassword, String newPassword)
   at Microsoft.CredentialManagement.OnPremisesPasswordReset.PasswordResetCredentialManager.ChangePassword(String changePasswordXMLRequestString)
```

## How to fix

The fix is to use an FQDN format, instead of the NETBIOS name in your AD-Connect configuration.

Open the Synchronization Service Manager -> Connectors -> open the Active Directory Domain Services window -> Configure Directory Partitions -> and under "Domain controller connection settings" click configure you'll see the list of DCs that ADConnect connects to and in which order. Make sure that your DC's are formatted in a Fully Qualified Domain Name like "[DC01.domain.com](https://dc01.domain.com/)".

Stay safe!
