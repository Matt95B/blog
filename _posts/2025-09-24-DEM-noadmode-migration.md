---
layout: post
title: Migrating Omnissa DEM from AD mode to No-AD mode
subtitle: A step-by-step guide for modernising your Dynamic Environment Manager (DEM) deployment
tags: [Horizon, DEM, Windows]
readtime: true
comments: true
author: Mathieu Beaugrand
---

## Overview

Omnissa [Dynamic Environment Manager (DEM)](https://techzone.omnissa.com/resource/what-dynamic-environment-manager) is a robust solution for managing user profiles and personalization across Windows virtual desktops and applications. It enables IT teams to deliver a consistent, tailored experience to users while maintaining centralized configuration control.

Traditionally, DEM has relied on **Active Directory (AD)** and **Group Policy Objects (GPOs)** for configuration delivery. With the rise of cloud-first and hybrid architectures, Omnissa introduced **No-AD mode**, removing the dependency on AD and simplifying deployment.

By following a staged migration plan, you can ensure minimal disruption and maintain full control over the user experience.

**Key steps include:**
- Preparing a valid `NoAD.xml`
- Reinstalling FlexEngine with the correct MSI switches
- Verifying policy application
- Rolling out in batches/rings

---

## AD vs. No-AD mode

### AD mode
- Uses Group Policy Objects (GPOs) for configuration  
- Requires Active Directory for targeting and policy delivery  
- Best suited for traditional on-premises environments  

### No-AD mode
- Uses a `NoAD.xml` config file stored on the configuration share  
- No dependency on AD or GPOs  
- Ideal for cloud-based or hybrid deployments  
- Configuration is driven by FlexEngine using the `NOADCONFIGFILEPATH` MSI property  

---

## Why migrate to No-AD mode?

Migrating to No-AD mode offers several advantages:

- **Simplified management** – No more GPO administration or AD filtering  
- **Cloud readiness** – Supports cloud-only and hybrid environments  
- **Faster deployment** – File-based configuration is easy to replicate, and logins are typically faster since GPO processing is reduced  
- **Improved flexibility** – Ideal for modern workspace use cases  

## Change considerations

### What changes
- **FlexEngine configuration mechanism:**  
  In No-AD mode, FlexEngine reads settings from a `NoAD.xml` file instead of GPOs.

- **Installation method:**  
  FlexEngine must be installed using the `NOADCONFIGFILEPATH` MSI property.

### What doesn’t change
Your existing configuration remains fully compatible:
- Configuration share  
- Profile archive share  
- Personalization settings  
- User Environment settings  
- Predefined/Default settings  

### Co-existence
- FlexEngine in No-AD mode *ignores DEM GPO settings*, enabling staged rollout by pool or OU.
- You do **not** need to remove GPOs immediately.

---

## Migration steps

### 1. Create a `NoAD.xml` configuration file
Use the [sample](https://docs.omnissa.com/bundle/DEMInstallConfigGuideV2309/page/SampleNoAD.xmlFile.html) provided by Omnissa to create your `NoAD.xml` file and place it on your DEM configuration share.

### 2. Enable No-AD mode on client machines

Reinstall the DEM agent in No-AD mode on your gold image or test ring devices:

```
msiexec /i "Omnissa Dynamic Environment Manager x64.msi" /qn /l*v %TEMP%\DEM-NoAD.log NOADCONFIGFILEPATH=\\FileSrv\DEMConfig$\General
```

- This enables No-AD mode.
- FlexEngine will now read `NoAD.xml` from the specified path.

### 3. Verify policy application
- Confirm Personalization and User Environment settings apply correctly
- Validate behavior across different user scenarios

### 4. Roll out in batches or rings
- Reinstall FlexEngine with the `NOADCONFIGFILEPATH` property on all target devices
- Since No-AD clients ignore DEM GPOs, GPOs may remain during migration
- Once all clients are migrated, unlink or disable DEM GPOs

### 5. Rollback (If needed)

If rollback is required:
1.	Uninstall FlexEngine
2.	Reinstall without the `NOADCONFIGFILEPATH` property
3.	Re-enable DEM GPOs

Since configuration and profile archives are unchanged, rollback is quick and low-risk.

---

## Final Thoughts

Migrating DEM from AD to No-AD mode is a strategic step for organizations moving toward cloud-first or hybrid models. It simplifies configuration management, reduces dependency on legacy infrastructure, and aligns with modern IT operations.

Before full deployment, ensure thorough testing in a staging environment and validate all Personalization and User Environment configurations.