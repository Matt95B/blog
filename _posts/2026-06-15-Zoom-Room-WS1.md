---
layout: post
title: Manage Windows based Zoom Rooms with Workspace ONE
description: Step by step guide to deploying and managing Zoom Rooms on Windows using Workspace ONE UEM
tags: [Workspace ONE, Windows, EntraID, UC]
author: Mathieu Beaugrand
---
*UNDER CONSTRUCTION*

## 1. Overview
Zoom Rooms provide a dedicated meeting room experience by transforming a Windows device into a purpose built collaboration endpoint. While Zoom offers its own management portal for application settings and room configuration, organisations still need a way to manage the underlying Windows operating system, security settings, and application lifecycle.

Workspace ONE provides a modern management framework for Windows based Zoom Rooms, enabling administrators to deploy devices, automate configuration, manage updates, and monitor compliance from a central console.

In this article, we’ll walk through the key steps required to onboard and manage Windows based Zoom Rooms using Workspace ONE.

---

## 2. Device Enrolment
Before deploying Zoom Rooms, it is important to determine how devices will be enrolled into Workspace ONE.

For new devices, I recommend leveraging Windows Autopilot to automate provisioning and enrolment. This approach allows devices to be shipped directly to site and automatically configured during the out-of-box experience.

If you are new to Autopilot and Workspace ONE, review my previous [blog post]({{site.url}}/2025-10-10-Autopilot-with-WS1) to setup the base of your autopilot config.

### 2.1 Entra ID device group
Create an Entra ID dynamic device group to automatically include all Windows based Room Room devices that are Autopilot registered in your tenant. This group will later be used to assign the Autopilot deployment profile.

- Log in to your Entra ID tenant - <https://portal.azure.com>
- Navigate to **Entra ID > Manage > Groups**
- Create a new group
  - Group type: **Security**
  - Group name: All Windows Zoom Room Autopilot devices
  - Group description: Security group containing all Windows based Zoom Room Autopilot devices
  - Microsoft Entra roles can be assigned to the group: **No**
  - Membership type: **Dynamic Device**
  - Rule syntax: `(device.devicePhysicalIds -any (_ -eq "[OrderID]:ZoomRoom"))`
    - ![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Entra-Group.png){:style="max-width: 300px; max-height: 500px;"}

### 2.2 Autopilot profile
Now it’s time to create and assign the Autopilot deployment profile.

- Log in to your Intune tenant - <https://intune.microsoft.com>
- Navigate to **Devices > Device onboarding > Enrollment > Windows > Windows Autopilot**
- Select **Deployment profiles**
- Click **Create profile**
  - Configure the profile according to your requirements
    - ![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Intune-Autopilot.png){:style="max-width: 300px; max-height: 500px;"}
  - Assign the profile to the Entra ID device group created in the previous step
    - ![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Intune-Autopilot-Assign.png){:style="max-width: 300px; max-height: 500px;"}
  - If you have another Autopilot profile that includes all your Autopilot devices, make sure to exclude the Zoom Room group from this profile to avoid conflict.
    - ![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Intune-Autopilot-Exclude.png){:style="max-width: 300px; max-height: 500px;"}

### 2.3 Autopilot devices
As devices are registered by your OEM, they will automatically appear in the Windows Autopilot devices list. Now to make sure the Zoom Room devices get assigned to the Entra ID group previously created they need to be tagged accordingly.

To tag you Zomm Room devices:
- Log in to your Intune tenant - <https://intune.microsoft.com>
- Navigate to **Devices > Device onboarding > Enrollment > Windows > Windows Autopilot**
- Select **Devices**
- Find the devices that are going to be used as Zoom Room in the list and assigned a `ZoomRoom` Group tag.
- ![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Intune-Autopilot-DevicesTag.png){:style="max-width: 300px; max-height: 500px;"}

---

## 3. Workspace ONE configuration

### 3.1 Organisation group
Consider creating a dedicated Organization Group (OG) for Zoom Rooms devices.
Benefits include:
- Dedicated application assignments
- Separate compliance policies
- Simplified reporting
- Granular administrator delegation
- Easier lifecycle management

### 3.2 Service account
Zoom Rooms devices are typically shared endpoints and should be managed using a device-centric management model rather than a user-centric enrollment process. However, Workspace ONE requires a user account to complete the enrollment process.

A common approach is to create a dedicated service account (for example, zoomroom@yourdomain.com) and use it to enroll all Zoom Rooms devices.

### 3.3 Applications
Use Workspace ONE to deploy and manage the applications required on your Zoom Rooms devices. Where possible, I recommend leveraging the Enterprise Application Repository (EAR) first, as it can significantly simplify application deployment and ongoing patch management by providing pre-packaged and vendor-maintained applications.

The specific applications you deploy will vary based on your requirements and room hardware, but common examples include:
- Zoom Rooms
- Workspace ONE Assist
- Workspace ONE Experience Management
- Endpoint Detection and Response (EDR) solutions
- Security and monitoring tools

Depending on your room hardware and peripherals, you may also need to deploy:
- Audio drivers
- Camera drivers
- Touch panel software
- Display management utilities
- Vendor-specific applications and drivers

![]({{site.url}}/images/2026-06-15-Zoom-Room-WS1/Intune-Autopilot-DevicesTag.png)

### 3.4 Device profiles
This is by no means an extended list of configuration but should be the baseline

#### 3.4.1 Kiosk mode
To lock down the device so it runs only the Zoom Rooms application, deploy a custom configuration profile:Navigate to Resources > Profiles & Baselines > Profiles > Add > Add Profile > Windows > Windows Desktop > Device Profile.Add the Custom Settings payload.Inject an OMA-DM XML configuration to map the custom shell to the Zoom Rooms executable (typically C:\Program Files\Zoom\ZoomRooms\bin\ZoomRooms.exe). This replaces the standard Windows Explorer shell with the Zoom interface upon login.

#### 3.4.2 Windows Security Baseline
Although Zoom Rooms devices are dedicated endpoints, they should still adhere to organisational security standards.
Recommended controls include:
- BitLocker encryption
- Firewall configuration
- Credential protection
- Device compliance monitoring

Though if you are using the templated baselines in Workspace ONE, a lot of the settings will be conflicitng with a Kiosk profile, so you will need to remove some of the pre-set settings.


#### 3.4.3 Power management
3. Power and Auto-Login Settings
To ensure the room system boots straight into the meeting software without requiring IT intervention:Configure Windows Kiosk or User accounts to auto-login using the local Zoom Room user credentials.Ensure BIOS/UEFI settings have Wake-on-LAN and "AC Power Recovery" enabled to turn the system on automatically following power outages.Configure Workspace ONE Windows power management profiles to prevent the device from sleeping or hibernating during inactive hours.

#### 3.4.4 Windows Updates
Workspace ONE can be used to manage Windows Update for Business policies, allowing administrators to:
* Control feature updates
* Control quality updates
* Configure maintenance windows
* Delay updates during critical business periods

## 4. Conclusion
Once configured, users simply walk into the meeting room and interact with the Zoom Rooms interface without needing access to the underlying Windows operating system.

By combining Zoom Rooms with Workspace ONE, organisations can deliver a secure, centrally managed meeting room experience while maintaining visibility and control over the Windows endpoint.

Workspace ONE complements Zoom Rooms by providing enterprise-grade device management, security, application deployment, and reporting capabilities. Together, these platforms help organisations streamline meeting room operations while ensuring devices remain secure, compliant, and easy to manage throughout their lifecycle.