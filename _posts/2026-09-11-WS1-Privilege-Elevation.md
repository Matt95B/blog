---
layout: post
title: Privilege elevation for Windows with Workspace ONE
description: Step by step guide to configuring Privilege Elevation profiles in Workspace ONE UEM
tags: [Workspace ONE, Windows, DEM, Essential 8]
author: Mathieu Beaugrand
---

## 1. Overview

Least privilege is one of those principles everyone agrees with in theory and quietly ignores in practice, usually because removing local admin rights breaks at least one business critical application. Some line of business apps write to `Program Files`, `ProgramData`, or `HKEY_LOCAL_MACHINE` every time they launch, not just during install, so a standard user gets an error the moment they open the app rather than during setup. The easy fix has always been to hand out local admin, and the easy fix is exactly why, in most security framework, "Restrict Administrative Privileges" maturity levels exist.

Workspace ONE UEM now ships a **Privilege Elevation** profile for Windows desktop devices, built on top of the elevation engine that has existed in Dynamic Environment Manager (DEM) for years. Rather than adding a user to the local Administrators group, you define rules that elevate specific applications, installers, or tasks, by hash, file path, publisher, or command-line argument, so only the thing that needs admin rights gets it.

{: .box-note}
**Important:** Privilege elevation grants temporary administrator rights to run a specific process. It is not a replacement for endpoint security controls, and it should be scoped as tightly as possible. Treat every rule as something that widens your attack surface a little, and only add what a genuine business case requires.

In this article, we'll configure a privilege elevation profile in Workspace ONE UEM and walk through a line of business application that simply refuses to run unless the user is a local administrator.

---

## 2. Prerequisites

Before configuring privilege elevation, check the following:

- Workspace ONE UEM console **26.02** or later.
- Devices enrolled with **Workspace ONE Intelligent Hub** for Windows, which carries the Dynamic Environment Manager (DEM) FlexEngine components that actually enforce elevation on the endpoint.
- A clear list of the specific executables, paths, or publishers you intend to elevate. Don't start this configuration without knowing exactly what you're elevating and why.

{: .box-note}
**Note:** When released this feature was only available with the Workspace ONE Enterprise or Advanced SKUs, but recently it was added to all SKUs supporting Desktop use cases. If you don't see the profile type in your console, confirm with your Omnissa account team that your subscription includes it.

---

## 3. Workspace ONE UEM configuration

### 3.1 Create the Privilege Elevation profile

- Log in to your Workspace ONE UEM tenant.
- Navigate to **Resources** > **Profiles & Baselines** > **Profiles** > **Add** and select **Add Profile**.
- Select **Windows** and then select **Windows Desktop**.
- Select **Device Profile**.
- Configure the **General** settings, including a descriptive name (for example, `Windows - Privilege Elevation - LOB Apps`) and an assignment group. I'd recommend starting with a small pilot Smart Group rather than assigning to all devices.
- Select the **Privilege Elevation** payload.

![]({{site.url}}/images/2026-09-11-WS1-Privilege-Elevation/WS1-Profile-PE.png)

### 3.2 Choose an elevation type

The payload exposes a number of elevation modes, but for an application that requires elevation every time it runs (rather than a one-off installer), path-based or hash-based rules are generally the better fit:

- Under **Privilege Elevation Type**, select **Path-based Elevated Application** (or **Hash** if you want to pin to an exact build).
- Paste the application's executable path and click **Add**
    - ![]({{site.url}}/images/2026-09-11-WS1-Privilege-Elevation/WS1-Profile-PE-Path.png){:style="max-width: 300px; max-height: 500px;"}
- Repeat for any additional executables the application depends on.
- Select **Save and Publish** to push the profile to your pilot assignment group.

<div class="box-note">
<p><strong>Note:</strong> Publisher-based rules remain the easiest to maintain long term, but only work if the vendor consistently signs their binaries, not every vendor does. To confirm, on a reference machine with the app installed, confirm the path and check whether the binary is signed:</p>
<code>Get-AuthenticodeSignature -FilePath "C:\Program Files\AppFolder\App.exe" | Select-Object Status, SignerCertificate | Format-List</code>
<p>If the binary isn't consistently signed across updates, or you'd rather pin to an exact known-good build, grab its hash instead:</p>
<code>Get-FileHash -Path "C:\Program Files (x86)\AppFolder\App.exe" -Algorithm SHA256</code>
<p>A hash-based rule pins to that exact file. Every time the vendor ships an update that replaces the executable, you'll need to refresh the hash, a path-based rule avoids this maintenance overhead if you're comfortable elevating anything running from that install location.</p>
</div>

---

## 4 Test

- On a pilot device logged in as a standard user, launch the app normally, no "Run as administrator" needed.
- Confirm the application opens and the company file loads without a data path or permissions error.

Once validated, expand the assignment to the rest of the devices, and repeat the same pattern for any other application in your environment with the same "must run as admin" problem.

---

## 5. Conclusion

Privilege Elevation profiles let you close the gap between "remove local admin everywhere" and "the business actually needs this one application to work," without falling back to blanket admin rights or fragile manual permission changes on install folders. By scoping rules to a specific hash, path, publisher, or argument combination, you keep the attack surface small while still solving genuine day to day friction.

As with any elevation mechanism, treat this as an operational tool rather than a security boundary. Keep rules narrow, review them periodically, and pair Privilege Elevation with your broader endpoint hardening (application control, EDR, and standard security frameworks) rather than as a substitute for them.