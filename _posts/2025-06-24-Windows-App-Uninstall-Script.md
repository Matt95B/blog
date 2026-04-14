---
layout: post
title: Uninstall Windows applications silently with PowerShell
subtitle: PowerShell script to uninstall Windows applications silently (where supported)
tags: [Windows, App, Script]
readtime: true
comments: true
author: Mathieu Beaugrand
---
When deploying Windows devices using **Autopilot**, unwanted **Win32** or **AppX** apps can clutter user experience or cause security risk. Below is a PowerShell script you can run in SYSTEM context to silently uninstall these apps as part of the **Out-of-Box Experience (OOBE)**.

This PowerShell script:
- Runs silently in the background without user interaction
- Handles **Win32 (MSI / EXE)** and **AppX** applications
- Supports **exact or partial** application name matching

---

## Gathering the application list
Before uninstalling anything, you need to know what’s installed. The following sections show how to list traditional desktop apps (Win32/MSI/EXE) from the registry and modern UWP/AppX packages using PowerShell.
If you already know which applications you want to remove, you can skip this section. Otherwise, use the scripts below to identify installed applications on a Windows device.

### List installed Win32 applications
Run the following script to list all **Win32 (MSI / EXE)** applications installed on the device.
```powershell
$RegistryPaths = @(
    "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*",
    "HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*"
)

foreach ($Path in $RegistryPaths) {
    Get-ItemProperty -Path $Path -ErrorAction SilentlyContinue |
    Where-Object {
        $_.DisplayName -and
        $_.UninstallString
    } |
    Select-Object `
        DisplayName,
        DisplayVersion,
        UninstallString
}
```

Make a note of the applications you want to remove. These names (exact or partial) will be used in the uninstall script later.

### List installed AppX applications
To list all removable AppX applications installed for all users on a Windows device, run:

```powershell
Get-AppxPackage -AllUsers |
Where-Object { $_.NonRemovable -eq $false } |
Select-Object Name, Version, PackageFullName
```

Make a note of the applications you want to remove. These names (exact or partial) will be used in the uninstall script later.

---

## Uninstall script
The script below takes an array of application display names (exact or wildcard) and:
- Discovers matching Win32 apps from the registry
- Discovers matching AppX packages for all users
- Executes silent uninstall or removal actions accordingly
- Safely exits if no matching applications are found

{: .box-note}
**Tip:** The script must be run in `SYSTEM` context

```powershell
# Define an array of app names (exact or partial match). For partial match use '*' symbol.
$DisplayNames = @(
    "AppName1",
    "AppName2*",
    "*AppName3*"
)

# Registry paths for Win32 apps
$RegistryPaths = @(
    "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*",
    "HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*"
)

# Collections
$FoundApps    = @()
$FoundAppx    = @()
$SeenMSIs     = @()
$MatchedNames = @()

# Discover Win32 apps
foreach ($Name in $DisplayNames) {
    foreach ($Path in $RegistryPaths) {

        Get-ItemProperty -Path $Path -ErrorAction SilentlyContinue |
        Where-Object { $_.DisplayName -like $Name } |
        ForEach-Object {
            if ($_.UninstallString -match "\{[A-F0-9\-]{36}\}") {
                $ProductCode = $matches[0]
                if ($SeenMSIs -notcontains $ProductCode) {
                    $SeenMSIs += $ProductCode
                    $FoundApps += [PSCustomObject]@{
                        DisplayName = $_.DisplayName
                        Type        = "MSI"
                        ProductCode = $ProductCode
                        Command     = $null
                    }
                }
                $MatchedNames += $Name
                return
            }

            if ($_.QuietUninstallString) {
                $FoundApps += [PSCustomObject]@{
                    DisplayName = $_.DisplayName
                    Type        = "EXE"
                    ProductCode = $null
                    Command     = $_.QuietUninstallString
                }
                $MatchedNames += $Name
                return
            }

            if ($_.UninstallString) {
                $FoundApps += [PSCustomObject]@{
                    DisplayName = $_.DisplayName
                    Type        = "EXE"
                    ProductCode = $null
                    Command     = "$($_.UninstallString) /S /norestart"
                }
                $MatchedNames += $Name
            }
        }
    }
}

# Discover AppX apps
foreach ($Name in $DisplayNames) {
    Get-AppxPackage -AllUsers |
    Where-Object { $_.Name -like $Name -or $_.PackageFullName -like $Name } |
    ForEach-Object {
        $FoundAppx += [PSCustomObject]@{
            DisplayName = $_.Name
            PackageName = $_.PackageFullName
        }
        $MatchedNames += $Name
    }
}

# Log missing apps
$MatchedNames = $MatchedNames | Select-Object -Unique
$MissingApps  = $DisplayNames | Where-Object { $_ -notin $MatchedNames }

foreach ($App in $MissingApps) {
    Write-Host "Application not found: $App" -ForegroundColor Yellow
}

# Exit if nothing found
if ($FoundApps.Count -eq 0 -and $FoundAppx.Count -eq 0) {
    Write-Host "No applications to uninstall." -ForegroundColor DarkYellow
    exit 0
}

# Uninstall Win32 apps silently
foreach ($App in $FoundApps) {
    Write-Host "Uninstalling Win32 app: $($App.DisplayName)"
    if ($App.Type -eq "MSI") {
        Start-Process -FilePath "msiexec.exe" `
            -ArgumentList "/x $($App.ProductCode) /qn /norestart" `
            -Wait -WindowStyle Hidden
    } else {
        Start-Process -FilePath "cmd.exe" `
            -ArgumentList "/c `"$($App.Command)`"" `
            -Wait -WindowStyle Hidden
    }
}

# Uninstall AppX apps
foreach ($Appx in $FoundAppx) {
    Write-Host "Removing AppX package: $($Appx.DisplayName)"
    try {
        Remove-AppxPackage -Package $Appx.PackageName -AllUsers -ErrorAction Stop
    } catch {
        Write-Host "Failed to remove AppX package: $($Appx.DisplayName)" -ForegroundColor Red
    }
}

exit 0
```
