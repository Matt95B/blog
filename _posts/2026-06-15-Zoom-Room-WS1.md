---
layout: post
title: Manage Zoom rooms via Workspace ONE
description: How to enrol and manage Windows based Zoom Rooms via Workspace ONE UEM
tags: [Workspace ONE, Zoom, Windows]
author: Mathieu Beaugrand
---
*UNDER CONSTRUCTION*

Autopilot
Setup the base of your autopilot config follow my previous [blog post]({{site.url}}/2025-10-10-Autopilot-with-WS1)

Device Group
Create an Entra ID group
Device group
Query

Autopilot profile
User-driven
assignment and exlusions

Autopilot device
Order ID: Zoom

Following those task, devices that are added to autopilot with the "Zoom" tag will automatically get added to the Ad Group and therefore be assigned the Self-driven autopilot profile.

1. App Deployment
Prepare the Zoom Room software installer (MSI or EXE) and upload it to the Workspace ONE UEM Console.Configure the Application Deployment settings and use the silent install switch (/qn /norestart for MSI) to push it directly to the Zoom Room device.

2. Configure Kiosk (Shell Launcher) Mode
To lock down the device so it runs only the Zoom Rooms application, deploy a custom configuration profile:Navigate to Resources > Profiles & Baselines > Profiles > Add > Add Profile > Windows > Windows Desktop > Device Profile.Add the Custom Settings payload.Inject an OMA-DM XML configuration to map the custom shell to the Zoom Rooms executable (typically C:\Program Files\Zoom\ZoomRooms\bin\ZoomRooms.exe). This replaces the standard Windows Explorer shell with the Zoom interface upon login.

3. Power and Auto-Login Settings
To ensure the room system boots straight into the meeting software without requiring IT intervention:Configure Windows Kiosk or User accounts to auto-login using the local Zoom Room user credentials.Ensure BIOS/UEFI settings have Wake-on-LAN and "AC Power Recovery" enabled to turn the system on automatically following power outages.Configure Workspace ONE Windows power management profiles to prevent the device from sleeping or hibernating during inactive hours.

4. Remote Management & Monitoring
Enroll the device using Workspace ONE Intelligent Hub.Use the Workspace ONE UEM Device Details page to monitor the Zoom Room PC’s status, connectivity, and OS patch compliance in real-time.If you need to troubleshoot AV or display issues on the room display remotely, you can leverage the Workspace ONE Assist Support for Windows Desktops tool to view and control the screen.