---
layout: post
title: Enhance your Google Chrome security with Chrome Enterprise
subtitle: Leverage the Google Admin Console to configure Google Chrome for your employees regardless of if you manage the device or not.
tags: [Chrome, EntraID, Workspace ONE, Essential 8]
readtime: true
comments: true
author: Mathieu Beaugrand
---
Nowadays, most applications can be accessed via a web browser instead of a tick client, making it obvious that securing the browser is becoming more important than ever. In recent years we have also seen an increase in popularity for Enterprise Browsers. In this article I am going to help you configure the most popular browser in the world, Google Chrome, in a enterprise setting to gain better control and visibility, regardless of if Chrome is running on a managed device or BYOD.

The good news is that you don't need to be a full-blown Google Workspace customer to implement this solution, you only need the **Chrome Enterprise Core** license (which is free) to get started. Though it is fair to say that a license upgrade to **Chrome Enterprise Premium** will give you advanced security control, including malware scanning, Data Loss Prevention (DLP) and much more. If you want to compare the two, refer to this [page](https://chromeenterprise.google/intl/en_au/products/chrome-enterprise-premium/).

## 1. Google Admin Console

### 1.1 Registration

If your organisation already uses Google services, your organisation should already have access to the Google Admin Console. If so, you would need to contact your Google Workspace Super Admin to gain access.

If your organisation is new to Google services, you will need to create a new account. I strongly recommend using a shared mailbox or alias for your primary account (ie. google.admin@yourdomain.com), so make sure to create it prior to registering for your Google services account.

To sign up go to the [Google Chrome Enterprise sign-up page](https://enterprise.google.com/signup/chrome-browser/email?origin=cbcm&utm_source=cbe-helpcenter&utm_campaign=cbe-helpcenter&utm_medium=helpcenter&utm_term=sign-up-for-cec&hl=en&pli=1).

### 1.2 Domain verification
Verify your domain ownership with Google so that no one else can use your domain without your permission.
- Login to your [Google Admin Console](https://admin.google.com)
- Go to **Account > Domains > Manage Domains**
- Click **Verify domain**
    - Copy the **TXT record** provided
- Open a new browser tab and login to your domain registrar
    - Add a new TXT record with the value copied earlier
- Go back to your Google Admin Console tab and click **Confirm**

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Domain.png)

### 1.3 Licenses
Next you will need to add the appropriate licenses in order to unlock the Chrome Enterprise features. To do so, go to **Billing > Subscriptions** and click **Buy or upgrade**.

At a minimum you will need to add **Chrome Enterprise Core** and **Cloud Identity Free**, note that those licenses are free.

If you want to implement **Chrome Enterprise Premium** features, you will also need to add **Chrome Enterprise Upgrade**, but note that this license comes at a cost. Alternaitve there are a few vendors that have bundled this license into their offering. [Omnissa Secure Access Suite](https://www.omnissa.com/insights/blog/omnissa-one-2025-amsterdam-google-chrome-enterprise-premium-secure-browser) is a perfect example of that, so reach out to your Omnissa reprensentative if you prefer to procure it that way.

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Licenses.png)

### 1.4 Services
Before you start synchronising users, it is probably a good idea to review which services to want to leave ON/OFF for each users once their Google account is created.
Go to **Apps > Google Workspace > Service status** and **Apps > Additional Google services**, and review the list of services want to leave ON or turn OFF for your users.

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Apps.png)

---

## 2. User synchronisation
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
| [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Add.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Add.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Authorise.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Authorise.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-User.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-User.png) |

| OU | User attributes | User activation |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-OU.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-OU.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserAttributes.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserActivation.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserActivation.png) |

| User decommission | User safeguard | Group scope |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-UserSafeguard.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Group.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-Group.png) |

| Group attributes | Group decommission | Group safeguard |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupAttributes.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png) |

---

## 3. SSO and federation
Now that we have Google accounts created, we need to think about how users are going to authenticate to it. The good news is, those Google accounts can be federated with your IdP of choice via SAML or OIDC, so users don't need to remember yet another password but instead use SSO.
If your primary IdP is EntraID, you are in luck as Google Workspace is federated by default with EntraID via OIDC as per the below screenshot.

However if you want to use another IdP, your can create a SSO profile. To do so, first you need to create an SSO profile under **Security > Authentication > SSO with third party IdP**, and then assign the SSO profile to an OU and/or user group.

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-SSO.png)

---

## 4. Google Chrome configuration

### 4.1 Chrome Enterprise Core
Now let's configure the foundation of your Chrome configuration.

- Go to **Chrome browsers > Settings**
- Select your top OU
- Under the **User & browser settings** tab search for `report`
- Configure the following reporting policies:
    - Managed browser reporting: **Enable managed browser cloud reporting**
    - Event reporting: **Enable event reporting**
    - Managed profile reporting: **Enable managed profile reporting for managed users**
    - Managed browser reporting upload frequency: **4 hours**
    - Device token management: **Delete token**
- Under the **User & browser settings** tab search for `safe browsing`
- Configure the following Safe Browsing policies:
    - Safe Browsing protection: Set the "Safe Browsing Protection Level" configuration to **enhanced mode**
    - Disable bypassing Safe Browsing warnings: **Do not allow users to bypass Safe Browsing warning**
    - Allow download deep scanning for Safe Browsing-enabled users: **Enable Safe Browsing download deep scans**
    - Download restrictions: **Block malicious downloads**

| Report | Safe Browsing |
|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Settings1.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Settings1.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Settings2.png)](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Settings2.png) |

### 4.2 Chrome extensions
Managing Chrome extentions is a key step toward securing your browser. 

- Go to **Chrome browsers > Apps & extensions**
- Under the **Users & browsers** tab
- Select the appropriate OU or Group for assignment
- Add and configure the Chrome extensions as per your requirements, for example:
    - Endpoint Verification extension
        - Click the **+** icon and select **Add Chrome app or extension by ID**
        - Extension ID: **callobklhcbilhphinckomhgkigmfocg**
        - Installation policy: **Force install + pin to browser toolbar** 
    - Secure Enterprise Browser extension:
        - Click the **+** icon and select **Add Chrome app or extension by ID**
        - Extension ID: **ekajlcmdfcigmdbphhifahdfjbkciflj**
        - Installation policy: **Force install + pin to browser toolbar** 

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Extension1.png)

Once you have visibility and understand better which extensions are being used in your environment, it is probably a good idea to then decide how you want to manage them moving forward, by either managing a blocklist or allowlist. For example:

- Go to **Chrome browsers > Apps & extensions**
- Under the **Settings** tab
- Select the appropriate OU or Group for assignment
- Click Allow/block mode
    - Play store: **Block all apps, admin manages allowlist**
    - Chrome Web Store: **Block all apps, admin manages allowlist, users may request extensions**

### 4.3 Chrome Enterprise Premium
Chrome Enterprise Premium comes with a lot of advanced configuration capabilities, so in this section I am just going to focus on setting up a handful of policies as a foundation, but feel free to explore the 500+ policies that are available.

- Go to **Chrome browsers > Settings**
- Select the appropriate OU or Group for assignment
- Under the **User & browser settings** tab search for `connector`
- Configure content connector and URL check policies:
    - Upload content analysis: **Chrome Enterprise Premium**
    - Additional settings
        - Delay file upload: **Delay the transfer until the analysis is complete**
            - Block file transfer on failure: **Enabled**
        - Check for sensitive data: **On by default, except for the following locations**
            - User justifications to bypass: **Enable**
        - Check for malware: **On by default, except for the following locations**
            - User justifications to bypass: **Disable**
        - Password protected files: **Allow**
        - Files larger than 50MB: **Allow**
    - Repeat the process for the following policies:        
        - Download content analysis
        - Bulk text content analysis
            - Minimum character count: 30
        - Print content analysis
        - Real time URL check
- Go to **Security > Access and data control > Data protection**
- Under the **Data protection settings** section
    - Data insights scanning and report: **On**

---

## 5. Management deployment
There are two main management mode for your Chrome browser, **Managed browser** and **Managed profile**.

Managed browser would typically apply to corporate devices that are managed by a PCLM/UEM tool as to make the browser managed a configuration file must be deployed to the device. This in turn allow for the Chrome browser to be managed regardless of if the user has signed in with their Google account or not.

Managed profile would typically apply to corporate devices and/or BYOD/Contractors, as the management is activated when the user creates a Chrome profile and then signs in with their managed Google account.

The two management types offer different benefits, if you want to read more about it visit this [page](https://support.google.com/chrome/a/answer/15591684).

### 5.1 Managed browser

### 5.1.1 Enrolment token
To configure the managed browser option, you need to download the configuration file from your Google Admin Console.

- Go to **Chrome browsers > Managed browsers**
- Select the devices OU
- Click **Enroll**
- Copy and/or download your token

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Google-Token.png)

### 5.1.2 Windows device enrolment
Use your PCLM/UEM tool to deploy the enrolment token on your corporate devices. In this instance I am leveraging Workspace ONE to deploy the configuration file.
- Login to your Workspace ONE console
- Go to **Resources > Profiles**
- Click **ADD** then **Add Profile**
- Select **Windows** then **Device Profile**
- Select the **Custom Settings** payload
    - Target: Workspace ONE Intelligent Hub
    - Install Settings: ```<wap-provisioningdoc id="1164DF07-F217-449B-95F8-FB85A34D3CA5" name="customprofile">/

<characteristic type="com.airwatch.winrt.registryoperation" uuid="4fa91319-eac0-4a16-9d10-093ba845b698">

  <parm RegistryPath="HKLM\SOFTWARE\Policies\Google\Chrome" Action="Replace">

    <Value Name="CloudManagementEnrollmentToken" Data="XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Type="String" />

    <Value Name="CloudManagementEnrollmentMandatory" Data="1" Type="DWORD" />

  </parm>

</characteristic>

</wap-provisioningdoc>```
    - Remove Settings: ```<wap-provisioningdoc id="1164DF07-F217-449B-95F8-FB85A34D3CA6" name="customprofile">/

<characteristic type="com.airwatch.winrt.registryoperation" uuid="4fa91319-eac0-4a16-9d10-093ba845b698">

  <parm RegistryPath="HKLM\SOFTWARE\Policies\Google\Chrome" Action="Remove">

    <Value Name="CloudManagementEnrollmentToken" Data="XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Type="String" />

    <Value Name="CloudManagementEnrollmentMandatory" Data="1" Type="DWORD"/>

  </parm>

</characteristic>

</wap-provisioningdoc>```
    - Save and Publish
    - The custom settings details are also documented [here](https://support.google.com/chrome/a/answer/9793780)

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/WS1-Profile.png)

### 5.2 Managed profile
The configuration for Chrome managed profile is already done as it simply relies on create a profile in Chrome and loging in with your managed Google account.

![](https://blog.beaugtech.com/assets/img/2026-01-16-Chrome-Enterprise/Chrome-Profile.png)

### 5.3 Verify the enrollment

Close Chrome browser on your test device, then reopen it to initiate the synchronisation. You can confirm the enrolment in the Google Admin console under **Chrome browser > Managed browsers** and/or **Chrome browser > Managed profile**. Additionally, on your test device to check if the policy is applied you can open a new browser tab and type `chrome://policy` in the URL section.

## 6. Additional info
- Chrome Enterprise training: <https://edu.exceedlms.com/student/collection/1771555>