---
layout: post
title: Enhance your Google Chrome security with Chrome Enterprise
subtitle: A practical guide to configuring Google Chrome Enterprise using the Google Admin Console, whether devices are managed or BYOD
tags: [Chrome, EntraID, Workspace ONE, Essential 8]
readtime: true
comments: true
author: Mathieu Beaugrand
---
Today, the majority of enterprise applications are accessed through a web browser rather than a thick client. As a result, **the browser has effectively become the new endpoint**, making browser security more critical than ever.

In parallel, we’ve seen a growing adoption of **Enterprise Browsers**, driven by the need for improved visibility, control, and data protection, especially in hybrid and BYOD environments.

In this article, I’ll walk through how to configure **Google Chrome Enterprise**, the most widely used browser globally, to provide enterprise-grade security and management. Importantly, this approach works regardless of whether the device itself is managed or personally owned.

The good news is that you don’t need to be a full Google Workspace customer to get started. **Chrome Enterprise Core** is completely free and provides strong foundational controls. For organisations requiring advanced security features such as **malware scanning**, **Data Loss Prevention (DLP)**, and **real-time URL protection**, **Chrome Enterprise Premium** is available as an upgrade.

You can compare Chrome Enterprise Core and Premium features on Google’s official comparison page:
<https://chromeenterprise.google/intl/en_au/products/chrome-enterprise-premium/>

## 1. Google Admin Console

### 1.1 Registration
If your organisation already uses Google services, access to the **Google Admin Console** likely already exists. In that case, contact your Google Workspace **Super Admin** to request access.

If your organisation is new to Google services, you’ll need to create a new Google Admin account.  
It’s strongly recommended to use a **shared mailbox or alias** (ie. `google.admin@yourdomain.com`) rather than an individual account.

To register, visit the Chrome Enterprise sign-up page: <https://enterprise.google.com/signup/chrome-browser/email>

### 1.2 Domain verification
Domain verification ensures no one else can register services using your organisation’s domain.
- Login to your [Google Admin Console](https://admin.google.com)
- Go to **Account > Domains > Manage Domains**
- Click **Verify domain**
    - Copy the **TXT record** provided
- Open a new browser tab and login to your domain registrar
    - Add a new TXT record with the value copied earlier
- Go back to your Google Admin Console tab and click **Confirm**

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Domain.png)

### 1.3 Licenses
To unlock Chrome Enterprise features:
- Go to **Billing > Subscriptions**
- Select **Buy or upgrade**

At a minimum, add:
- **Chrome Enterprise Core** (Free)
- **Cloud Identity Free**

For advanced security capabilities, add:
- **Chrome Enterprise Upgrade** (Paid)

Some vendors bundle Chrome Enterprise Premium into their offerings. For example, [Omnissa Secure Access Suite](https://www.omnissa.com/insights/blog/omnissa-one-2025-amsterdam-google-chrome-enterprise-premium-secure-browser) includes Chrome Enterprise Premium as part of the solution.

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Licenses.png)

### 1.4 Services review
Before synchronising users, review which Google services should be enabled or disabled.

Navigate to:
- **Apps > Google Workspace > Service status**
- **Apps > Additional Google services**

Disable unnecessary services to reduce exposure and complexity.

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Apps.png)

---

## 2. User synchronisation
Google supports multiple user provisioning methods. In this guide, we’ll use **SCIM provisioning from Microsoft Entra ID**, leveraging the native integration in the Google Admin Console.

{: .box-note}
**Note:** At the time of writing, this integration is in *beta*.
Alternatively, you can use the **Google Cloud / G Suite Connector by Microsoft** app from the Entra ID app gallery: <https://learn.microsoft.com/en-us/entra/identity/saas-apps/g-suite-provisioning-tutorial>

### 2.1 SCIM configuration
- Login to your [Google Admin Console](https://admin.google.com)
- Go to **Directory > Directory sync**
- Click **Add Azure Active Directory**
- Give it a name then click **Authorise and Save**
- You will get redirected to EntraID for authentication
    - Note that you will require to authenticate with a Global Admin account in order to authorise the integration
- Accept the **Google Directory Sync** app creation in EntraID

| SCIM Add | SCIM Authorise |
|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Add.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Add.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Authorise.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Authorise.png) |

### 2.2 User sync
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

| User scope | OU | User attributes |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-User.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-User.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-OU.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-OU.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserAttributes.png) |

| User activation | User decommission | User safeguard |
|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserActivation.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserActivation.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-UserSafeguard.png) |

### 2.3 Group sync
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

| Group scope | Group attributes | Group decommission | Group safeguard |
|:-------------------:|:-------------------:|:-------------------:|:-------------------:|
| [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Group.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-Group.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupAttributes.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupAttributes.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupDecom.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupDecom.png) | [![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png)](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SCIM-GroupSafeguard.png) |

---

## 3. Single Sign-On (SSO) and federation
With Google accounts created, the next step is authentication.

Google Workspace supports federation using **OIDC or SAML**, allowing users to authenticate using your existing Identity Provider (IdP).

If your IdP is **Microsoft Entra ID**, federation is enabled by default using OIDC.

If using another IdP:
- Navigate to **Security > Authentication > SSO with third-party IdP**
- Create an SSO profile
- Assign it to an OU or user group

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-SSO.png)

---

## 4. Google Chrome configuration

### 4.1 Chrome Enterprise Core
Now let's configure the foundation of your Chrome configuration.

#### 4.1.1 Reporting
- Go to **Chrome browsers > Settings**
- Select your top OU
- Under the **User & browser settings** tab search for `report`
- Configure the following reporting policies:
    - Managed browser reporting: **Enable managed browser cloud reporting**
    - Event reporting: **Enable event reporting**
    - Managed profile reporting: **Enable managed profile reporting for managed users**
    - Managed browser reporting upload frequency: **4 hours**
    - Device token management: **Delete token**

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Settings1.png)

#### 4.1.2 Safe browsing
- Under the **User & browser settings** tab search for `safe browsing`
- Configure the following Safe Browsing policies:
    - Safe Browsing protection: Set the "Safe Browsing Protection Level" configuration to **enhanced mode**
    - Disable bypassing Safe Browsing warnings: **Do not allow users to bypass Safe Browsing warning**
    - Allow download deep scanning for Safe Browsing-enabled users: **Enable Safe Browsing download deep scans**
    - Download restrictions: **Block malicious downloads**

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Settings2.png)

### 4.2 Chrome extensions
Extension management is critical to browser security.

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

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Extension1.png)

Once you gain visibility and understand better which extensions are being used in your environment, it is probably a good idea to then decide how you want to manage them moving forward, by either managing a blocklist or allowlist. For example:

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

Repeat the above process for the following policies:        
- Download content analysis
- Bulk text content analysis
    - Minimum character count: 30
- Print content analysis
- Real time URL check

Enable advanced data protection features:
- Go to **Security > Access and data control > Data protection**
- Under the **Data protection settings** section
    - Data insights scanning and report: **On**

---

## 5. Deployment models
Chrome supports two management modes:

**Managed browser**
- Device-level management
- Ideal for corporate-managed devices
- Policies apply even without user sign-in

**Managed profile**
- User-based management
- Ideal for BYOD and contractors
- Activated when the user signs in to Chrome

More information:
<https://support.google.com/chrome/a/answer/15591684>

### 5.1 Managed browser

#### 5.1.1 Enrolment token
To configure the managed browser option, you need to download the configuration file from your Google Admin Console.

- Go to **Chrome browsers > Managed browsers**
- Select the devices OU
- Click **Enroll**
- Copy and/or download your token

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Google-Token.png)

#### 5.1.2 Windows deployment
Deploy the enrolment token using your PCLM/UEM solution. In this instance I am leveraging Workspace ONE to deploy the configuration file.
- Login to your Workspace ONE console
- Go to **Resources > Profiles**
- Click **ADD** then **Add Profile**
- Select **Windows** then **Device Profile**
- Select the **Custom Settings** payload
    - Target: **Workspace ONE Intelligent Hub**
    - Install Settings:

        ```
        <wap-provisioningdoc id="1164DF07-F217-449B-95F8-FB85A34D3CA5" name="customprofile">/
        <characteristic type="com.airwatch.winrt.registryoperation" uuid="4fa91319-eac0-4a16-9d10-093ba845b698">
         <parm RegistryPath="HKLM\SOFTWARE\Policies\Google\Chrome" Action="Replace">
           <Value Name="CloudManagementEnrollmentToken" Data="XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Type="String" />
           <Value Name="CloudManagementEnrollmentMandatory" Data="1" Type="DWORD" />
         </parm>
        </characteristic>
        </wap-provisioningdoc>
        ```

    - Remove Settings:

        ```
        <wap-provisioningdoc id="1164DF07-F217-449B-95F8-FB85A34D3CA6" name="customprofile">/
        <characteristic type="com.airwatch.winrt.registryoperation" uuid="4fa91319-eac0-4a16-9d10-093ba845b698">
         <parm RegistryPath="HKLM\SOFTWARE\Policies\Google\Chrome" Action="Remove">
           <Value Name="CloudManagementEnrollmentToken" Data="XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Type="String" />
           <Value Name="CloudManagementEnrollmentMandatory" Data="1" Type="DWORD"/>
         </parm>
        </characteristic>
        </wap-provisioningdoc>
        ```

    - Save and Publish
    - Custom settings details are documented [here](https://support.google.com/chrome/a/answer/9793780)

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/WS1-Profile.png)

### 5.2 Managed profile
No additional configuration required, users simply:
- Create a Chrome profile
- Sign in with their managed Google account

![](https://blog.beaugtech.com/assets/img/2026-01-15-Chrome-Enterprise/Chrome-Profile.png)

### 5.3 Confirm enrolment

On your test device restart Chrome to initiate the synchronisation. Confirm the enrolment in the Google Admin console under **Chrome browser > Managed browsers** and/or **Chrome browser > Managed profile**. Additionally, on your test device, to check if the policy is applied you can open a new browser tab and type `chrome://policy` in the URL section.

---

## 6. Additional resources
- Chrome Enterprise training: <https://edu.exceedlms.com/student/collection/1771555>