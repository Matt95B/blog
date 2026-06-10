---
layout: post
title: Configure Windows Autopilot with Workspace ONE
description: Step-by-step guide to configuring Windows Autopilot with Workspace ONE and Microsoft Entra ID
tags: [Windows, Workspace ONE, EntraID]
author: Mathieu Beaugrand
---

## Overview

Windows Autopilot is a collection of technologies designed to simplify the deployment and provisioning of new Windows devices. It enables devices to be pre-configured and automatically enrolled into your preferred Unified Endpoint Management (UEM) platform as soon as they are powered on and connected to the internet.

To take advantage of Autopilot, your OEM or hardware reseller should pre-register devices to your Entra ID tenant during the fulfilment process. Be sure to discuss this requirement with your vendor when placing your device orders.

Autopilot is one of several methods available for enrolling Windows devices into Workspace ONE. For a complete list of supported enrollment options, refer to the Omnissa documentation [here](https://docs.omnissa.com/bundle/Windows_Desktop_ManagementVSaaS/page/uemWindeskDeviceEnrollment.html).

---

## Entra ID integration
### Workspace ONE UEM configuration
The first step is to integrate your Workspace ONE UEM environment with Microsoft Entra ID.

- Log in to your Workspace ONE UEM tenant.
- Navigate to **Settings > All Settings > System > Enterprise Integration > Directory Services**
- Configure the following settings:
  - Azure AD Integration: **Enabled**
  - Directory ID: *Your_Entra ID_Tenant_ID*
    - Your Tenant ID can be found in the Entra admin centre under **Entra ID > Manage > Properties**
  - Use Azure AD For Identity Services: **Enabled**
  - Immutable ID Mapping Attribute: `ms-DS-ConsistencyGuid`
  - Mapping Attribute Data Type: **Binary**
  - Windows OOBE: **Enabled**
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/WS1-Entra.png){:style="max-width: 300px; max-height: 500px;"}
- Under the Azure Active Directory section, copy the MDM Discovery URL and MDM Terms of Use URL.
  - These values will be required in the next section.
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/WS1-MDMURL.png){:style="max-width: 300px; max-height: 500px;"}

### MDM authority
Next, configure Workspace ONE as the MDM authority within Microsoft Entra ID.
- Log in to your Entra ID tenant - <https://portal.azure.com>
- Navigate to **Entra ID > Manage > Mobility (MDM and WIP)**
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Entra-MDM.png){:style="max-width: 300px; max-height: 500px;"}
- Click **Add application**
- Search for **AirWatch by Omnissa** and click **Create**
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Entra-Search.png){:style="max-width: 300px; max-height: 500px;"}
  - Configure the following settings:
    - MDM User Scope: **All**
    - MDM Discovery URL: *Paste the URL copied earlier*
    - MDM Terms of Use URL: *Paste the URL copied earlier*
    - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Entra-AirWatch.png){:style="max-width: 300px; max-height: 500px;"}

{: .box-note}
**Note:** If Microsoft Intune is also configured in your tenant, ensure its **MDM User Scope** is set to **None** to prevent enrolment conflicts.

---

## Autopilot configuration

### Entra ID device group
Create a dynamic device group to automatically include all Windows Autopilot devices registered in your tenant. This group will later be used to assign the Autopilot deployment profile.

- Log in to your Entra ID tenant - <https://portal.azure.com>
- Navigate to **Entra ID > Manage > Groups**
- Create a new group
  - Group type: **Security**
  - Group name: All Windows Autopilot devices
  - Group description: Security group containing all Windows Autopilot devices
  - Microsoft Entra roles can be assigned to the group: **No**
  - Membership type: **Dynamic Device**
  - Rule syntax: `(device.devicePhysicalIDs -any (_ -startsWith "[ZTDid]"))`
    - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Entra-Group.png){:style="max-width: 300px; max-height: 500px;"}

The above rule automatically includes all devices registered with Windows Autopilot.

If you require more granular targeting, Microsoft provides additional examples and guidance for Autopilot dynamic membership rules in their [documentation](https://learn.microsoft.com/en-us/autopilot/enrollment-autopilot).

### Autopilot profile

Now it’s time to create and assign the Autopilot deployment profile.

- Log in to your Intune tenant - <https://intune.microsoft.com>
- Navigate to **Devices > Device onboarding > Enrollment > Windows > Windows Autopilot**
- Select **Deployment profiles**
- Click **Create profile**
  - Configure the profile according to your requirements
    - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Intune-Autopilot.png){:style="max-width: 300px; max-height: 500px;"}
  - Assign the profile to the Entra ID device group created in the previous step
    - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Intune-Autopilot-Assign.png){:style="max-width: 300px; max-height: 500px;"}

{: .box-note}
**Tip:** If you cannot access the Autopilot menus shown above, it is likely because your tenant does not have an Intune license assigned. Microsoft now restricts access to these pages based on licensing. Purchasing a single **Microsoft Intune Device Plan 1** license is typically sufficient to unlock the required administration pages.

### Autopilot devices
As devices are registered by your OEM, they will automatically appear in the Windows Autopilot devices list. Because the deployment profile is assigned to the dynamic Entra ID group, newly registered devices will automatically receive the Autopilot configuration without any manual intervention.

To view registered devices:
- Log in to your Intune tenant - <https://intune.microsoft.com>
- Navigate to **Devices > Device onboarding > Enrollment > Windows > Windows Autopilot**
- Select **Devices**
- ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/Intune-Autopilot-Devices.png){:style="max-width: 300px; max-height: 500px;"}

---

## Workspace ONE considerations

There are a few additional Workspace ONE settings worth reviewing to optimise the Autopilot onboarding experience.

### Intelligent Hub Deployment
Ensure that Workspace ONE Intelligent Hub is automatically deployed during enrollment.
- Log in to your Workspace ONE UEM tenant
- Navigate to **Settings > All Settings > Devices & Users > Microsoft > Windows > Intelligent Hub Application**
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/WS1-Hub.png){:style="max-width: 300px; max-height: 500px;"}

### Status Tracking Page
To provide users with better visibility during onboarding, I recommend enabling the Workspace ONE Status Tracking page during Out-of-Box Experience (OOBE).
- Log in to your Workspace ONE UEM tenant
- Navigate to **Settings > All Settings > Devices & Users > General > Enrollment > Optional Prompt**
  - ![]({{site.url}}/images/2025-10-10-Autopilot-with-WS1/WS1-Hub.png){:style="max-width: 300px; max-height: 500px;"}

This provides users with a clear view of application installations, profile deployments, and configuration progress during device provisioning.

## User experience
*Coming soon*