---
title: "ADMX ingestion for Centero Agent and Carillon Client using Intune"
date: 2022-02-20
categories: 
  - "knowledgebase"
coverImage: "1645388901.png"
---

This article is about the ADMX templates for Centero Agent and Carillon client, that you can use to configure the settings on your endpoints. Microsoft Endpoint Manager (Intune) is capable of ADMX ingestion, but this process can be complex sometimes. This article will explain the ADMX ingestion and has a couple of examples, on how to handle various settings.

## ADMX ingestion

Before the client can use settings from the ADMX template, you need to ingest them with Microsoft Endpoint Manager/Intune

  
**TIP:** Use an editor like Notepad or Visual Studio code to explore the ADMX template. You will need the content of the ADMX template, and need it as a reference to build the policies. ADMX templates can be found in the \\ADMX Templates directory on installation media. In Microsoft Endpoint Manager, create a new custom profile for Windows 10 and later.

![](/assets/images/image-11.png)

Under OMA-URI settings, create a new row.

![](/assets/images/image-13.png)

1. Enter a name, for example “Centero ADMX ingestion”
2. Enter a description
3. Use this OMA-URI: _./Device/Vendor/MSFT/Policy/ConfigOperations/ADMXInstall/Centero/Policy/CenteroAdmx_
4. Pick **String** as your data type
5. Paste the entire content of the .admx file in the value property.

Apply the setting to one of your test systems, and check of the policy is ingested under **_Computer\\HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\PolicyManager\\AdmxDefault\\{GUID}_**  

![](/assets/images/image-14.png)

## Centero Agent and Carillon settings

After the ADMX template is ingested, you can continue to configure the settings. There are a couple of important values that you need to build your settings.

For example, if you want to set the gateway address, the policy would look like this:

![](/assets/images/image-15.png)

Let’s start with the OMA-URI. The first part is static, and always the same: **_./Device/Vendor/MSFT/Policy/ Centero~Policy~Cat\_Centero\_9~_**

Next comes the category, which will depend on your setting. The gateway setting is part of the **_CAT\_446435B1\_DCCD\_4C46\_8AC1\_976E589689AB_** category. You can find that in the ADMX template (2)

![](/assets/images/image-16.png)

Next comes the policy itself, which you will also find in the ADMX template (1). In this case that is **_POL\_622F9116\_26DE\_40D8\_9E2E\_E8952ED5458E_**

Putting it all together, your OMA-URI will look like this: OMA=URI: **_./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~CAT\_446435B1\_DCCD\_4C46\_8AC1\_976E589689AB/POL\_622F9116\_26DE\_40D8\_9E2E\_E8952ED5458E_**

In this case, you need to pick ‘_String_’ as your data type, but some settings will also support _Boolean_ or _Integer_ as well. 

The value of the setting will hold the data-id parameter, which you can find in the ADMX template as well (3) This might be named differently, depending on what setting you use. Most of the time it is referred to as ‘text id’, ‘enum-id’,  or ‘decimal id’. In this case that value is **TXT\_EC78A049\_4300\_4489\_8CF1\_275A2B4D0901.** The value itself is the address of your gateway.

The value will look like this:_<enabled/> <data id="TXT\_EC78A049\_4300\_4489\_8CF1\_275A2B4D0901" value="https://gateway.com/AgentGateway.asmx"/>_

## Categories

Polices are divided into three different categories. Each category has its own prefix.

| Carillon Client | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13 |
| --- | --- |
| Centero Agent | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~CAT\_446435B1\_DCCD\_4C46\_8AC1\_976E589689AB |
| Centero Service | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~CAT\_DD93EB8C\_8C0E\_4A33\_9D15\_3E948772FF72 |

## Examples

A couple of samples for the most used settings. Here you can see the different types of categories, policies, and data types.  

| **Name** |  Set usage scenario |
| --- | --- |
| **Description** |  0=login screen only 1=user context only 2=both |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13/Policy\_Credential\_provider\_2812897 |
| **Data type** |  String |
| **Value** |  <enabled/> <data id="Policy\_DropList\_Element\_Credential\_provider\_2812900" value="1"/> |
| **Notes** | By default, the Carillon screen shows up on both the login screen and user context. |

* * *

| **Name** |  Gateway address |
| --- | --- |
| **Description** |  Set gateway address |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~CAT\_446435B1\_DCCD\_4C46\_8AC1\_976E589689AB/POL\_622F9116\_26DE\_40D8\_9E2E\_E8952ED5458E |
| **Data type** |  String |
| **Value** |  <enabled/> <data id="TXT\_EC78A049\_4300\_4489\_8CF1\_275A2B4D0901" value="https: //centero.com/AgentGateway.asmx"/> |

* * *

| **Name** |  Credential provider show use activation code |
| --- | --- |
| **Description** |  Credential provider show use activation code |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13/POL\_4791A655\_C211\_4F64\_AF29\_E709F4F93E01 |
| **Data type** |  String |
| **Value** |  <enabled/> or <disabled/> |
| **Notes** | This setting will enable or disable the use of the activation code. By default, this is enabled. |

* * *

| **Name** |  Credential provider show use local account |
| --- | --- |
| **Description** |  Credential provider show use local account |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13/POL\_2AA87D01\_3385\_4AA4\_A8BD\_7C3F08BA2C15 |
| **Data type** |  String |
| **Value** |  <enabled/> or <disabled/> |
| **Notes** | This setting will enable or disable the use of a local account. By default, this is enabled. |

* * *

| **Name** |  Credential provider show use domain account |
| --- | --- |
| **Description** |  Credential provider show use domain account |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13/POL\_72C9F936\_DEEB\_4B36\_9C57\_3508E041EC95 |
| **Data type** |  String |
| **Value** |  <enabled/> or <disabled/> |
| **Notes** | This setting will enable or disable the use of a domain account. By default, this is enabled. |

* * *

| **Name** |  Credential provider default activation type |
| --- | --- |
| **Description** |  1=domain account 2=local account 3=activation code |
| **OMA-URI** | ./Device/Vendor/MSFT/Policy/Config/Centero~Policy~Cat\_Centero\_9~Cat\_Carillon\_11~Cat\_Client\_13/POL\_CDE2AE07\_7B33\_4B56\_9519\_C9FBD5A08836 |
| **Data type** |  String |
| **Value** |  <enabled/> <data id="DST\_4E56F178\_A4F2\_4A93\_BD0E\_914E221A8FFF" value="1"/> |
| **Notes** | Configure the default activation type. |

## Troubleshoot and tips

When the settings are applied, they will show up under **_Computer\\HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Policies\\Centero_**

By using the Event Viewer, you can see if the settings are applied correctly: **_Microsoft-Windows-DeviceManagement-Enterprise-Diagnostics-Provider/Admin_**
