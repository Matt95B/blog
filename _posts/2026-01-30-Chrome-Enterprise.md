---
layout: post
title: Enhance your Google Chrome security with Chrome Enterprise
subtitle: Leverage the Google Admin Console to configure Google Chrome for your employees regardless of if you manage the device or not.
tags: [Chrome, EntraID]
readtime: true
comments: true
author: Mathieu Beaugrand
---

![](https://blog.beaugtech.com/assets/img/under-construction.jpg)

Nowadays, most applications can be accessed via a web browser instead of a tick client, making it obvious that securing the browser is becoming more important than ever. In recent years we have also seen an increase in popularity for Enterprise Browsers. In this article I am going to help you configure the most popular browser in the world, Google Chrome, in a enterprise setting to gain better control and visibility, regardless of if Chrome is running on a managed device or BYOD.

The good news is that you don't need to be a full-blown Google Workspace customer to implement this solution, you only need the **Chrome Enterprise Core** license (which is free) to get started. Though it is fair to say that a license upgrade to **Chrome Enterprise Premium** will give you advanced security control, including malware scanning, Data Loss Prevention (DLP) and much more. If you want to compare the two, refer to this [page](https://chromeenterprise.google/intl/en_au/products/chrome-enterprise-premium/).

## Google Admin Console

### Registration

If your organisation already uses Google services, your organisation should already have access to the Google Admin Console. If so, you would need to contact your Google Workspace Super Admin to gain access.

If your organisation is new to Google services, you will need to create a new account. I strongly recommend using a shared mailbox or alias for your primary account (ie. google.admin@yourdomain.com), so make sure to create it prior to registering for your Google services account.

To sign up go to the [Google Chrome Enterprise sign-up page](https://enterprise.google.com/signup/chrome-browser/email?origin=cbcm&utm_source=cbe-helpcenter&utm_campaign=cbe-helpcenter&utm_medium=helpcenter&utm_term=sign-up-for-cec&hl=en&pli=1).

### Domain verification
Verify your domain ownership with Google so that no one else can use your domain without your permission.
- Login to your [Google Admin Console](https://admin.google.com)
- Go to **Account > Domains > Manage Domains**
- Click **Verify domain**
    - Copy the **TXT record** provided
- Open a new browser tab and login to your domain registrar
    - Add a new TXT record with the value copied earlier
- Go back to your Google Admin Console tab and click **Confirm**

![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-Domain.png)

### Licenses
Next you will need to add the appropriate licenses in order to unlock the Chrome Enterprise features. To do so, go to **Billing > Subscriptions** and click **Buy or upgrade**.

At a minimum you will need to add **Chrome Enterprise Core** and **Cloud Identity Free**, note that those licenses are free.

If you want to implement **Chrome Enterprise Premium** features, you will also need to add **Chrome Enterprise Upgrade**, but note that this license comes at a cost.

![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-Licenses.png)

### Services
Before you start synchronising users, it is probably a good idea to review which services to want to leave ON/OFF for each users once their Google account is created.
Go to **Apps > Google Workspace > Service status** and **Apps > Additional Google services**, and review the list of services want to leave ON or turn OFF for your users.

![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-Apps.png)

---

## User synchronisation from EntraID
Google offers a few different ways to synchronise users to Google Workspace, and what tool to use will come down to your Identity solution. In this instance, I will use **SCIM provisioning** from EntraID to Google Workspace leveraging the native integration available in the Google Admin Console. Note that at the time of writing, this option is still in *beta*, so if you prefer instead you can use **Google Cloud / G Suite Connector by Microsoft** app in the Microsoft Entra App Gallery, then leverage the automated provisioning feature in EntraID. This process is documented [here](https://learn.microsoft.com/en-us/entra/identity/saas-apps/g-suite-provisioning-tutorial).

To enable SCIM provisioning from EntraID to Google Workspace, follow the below steps:
- Go to **Directory > Directory sync**
- Click **Add Azure Active Directory**
- Give it a name then click **Authorise and Save**
- You will get redirected to EntraID for authentication
    - Note that you will require to authenticate with a Global Admin account in order to authorise the integration
- Accept the **Google Directory Sync** app creation in EntraID
- Under the **User sync** section, click **Set up user sync**
    - User Scope
        - Use a user based EntraID security group
        - Copy the name of your EntraID group and paste it into the field
        - Click Verify
        - Note that nested groups are supported
    - Organizational unit (OU) selection
        - Select **Place users in a specific OU**
        - Click on **Select organizational unit**
        - Then select the top level OU of your domain
    - User attribute mapping
        - Map the user attributes as per your requirements
            - [Common user attribute mappings](https://support.google.com/a/answer/10344342?hl=en#step2&zippy=step-map-the-user-attributes)
    - Account activation
        - Select **Don't send activation email**
    - Deprovisioning
        - Click **Suspend user in Google Directory**
    - Safeguards
        - Set the safeguard as per your requirements
- Under the **Group sync** section, click **Set up group sync**
    - Synchronising groups will allow you to assign different Chrome policies based on user groups (ie. Executives vs Staff)
    - Group scope
        - Select **Sync selected groups**
        - Copy the name of your EntraID group and paste it into the field
        - Click Verify
        - Note that for a group to sync, the group must be a mail-enabled security group (see required group attributes below)
    - Required attributes
        - Leave the default values
    - Deprovisioning
        - Click **Delete group in your Google Directory**
    - Safeguards
        - Set the safeguard as per your requirements

| SCIM Add | SCIM Authorise | User scope |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Add.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Add.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Authorise.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Authorise.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-User.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-User.png) |

| OU | User attributes | User activation |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-OU.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-OU.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserAttributes.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserActivation.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserActivation.png) |

| User decommission | User safeguard | Group scope |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-UserSafeguard.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Group.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-Group.png) |

| Group attributes | Group decommission | Group safeguard |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupAttributes.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-30-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png) |

---

## SSO and federation

---

## Google Chrome configuration

### Chrome Enterprise Core

### Chrome Enterprise Premium

---

## Device Management

To check how the policy is applied on the device you can open a new browser tab and type <chrome://policy> in the URL section.