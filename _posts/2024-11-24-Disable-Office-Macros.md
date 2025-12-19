---
layout: post
title: Disable Microsoft Office macros
subtitle: A step-by-step guide for disabling Microsoft Office macros 
tags: [Workspace ONE, Office, Macros, Windows, macOS, Script]
readtime: true
comments: true
author: Mathieu Beaugrand
---

Have you been asked to disable Office macros for all or most users to reduce your threat attack surface?

I have built Workspace ONE UEM scripts to achieve this and thought I would share the details here.


## Windows configuration

### Script
In Workspace ONE UEM console, create a **script** and paste the below code into the code field.

- Name: Disable VBA for Office
- Run: System context

```
# Disabling VBA for all office apps
# Define VBA variables
$keyPath1 = "HKLM\SOFTWARE\Policies\Microsoft\Office\16.0\Common"
$valueName1 = "vbaoff"
$valueData1 = 1

# Check if the registry key exists
if (Test-Path "Registry::$keyPath1") {
    Set-ItemProperty -Path "Registry::$keyPath1" -Name $valueName1 -Value $valueData1
    Write-Host "Registry key '$valueName1' has been set to '$valueData1'."
} else {
# Create the registry key if it doesn't exist
    New-Item -Path "Registry::$keyPath1" -Force | Out-Null
    Set-ItemProperty -Path "Registry::$keyPath1" -Name $valueName1 -Value $valueData1
    Write-Host "Registry key '$valueName1' has been created and set to '$valueData1'."
}
```

To enhence user experience, you might also want to disable the notification that could appear in Office.

In Workspace ONE UEM console, create a **script** and paste the below code into the code field.

- Name: Disable VBA notifications for Office
- Run: User context with admin right

```
# Define the registry key path for each Office application
$officeApplications = @{
    "Excel" = "HKCU\Software\Policies\Microsoft\Office\16.0\Excel\Security"
    "Word" = "HKCU\Software\Policies\Microsoft\Office\16.0\Word\Security"
    "PowerPoint" = "HKCU\Software\Policies\Microsoft\Office\16.0\PowerPoint\Security"
    "Access" = "HKCU\Software\Policies\Microsoft\Office\16.0\Access\Security"
    "Outlook" = "HKCU\Software\Policies\Microsoft\Office\16.0\Outlook\Security"
    "Publisher" = "HKCU\Software\Policies\Microsoft\Office\16.0\Publisher\Security"
    "Visio" = "HKCU\Software\Policies\Microsoft\Office\16.0\Visio\Security"
}

# Define the registry value name and data
# 1=Enable VBA macros, 2=Disable VBA macros with notification, 3=Disable VBA macros except digitally signed, 4=Disable VBA macros without notification
$valueName = "VBAWarnings"
$valueData = 4

# Loop through each Office application and set the registry value
foreach ($app in $officeApplications.GetEnumerator()) {
    $keyPath = $app.Value
    $appName = $app.Key

# Check if the registry key exists
    if (Test-Path "Registry::$keyPath") {
        Set-ItemProperty -Path "Registry::$keyPath" -Name $valueName -Value $valueData
    } else {
# Create the registry key if it doesn't exist
        New-Item -Path "Registry::$keyPath" -Force | Out-Null
        Set-ItemProperty -Path "Registry::$keyPath" -Name $valueName -Value $valueData
    }

    Write-Host "Disabled macros notifications for $appName."
}
```

### Reporting
Report on Microsoft Office VBA macros status on Windows via a PowerShell script.

In Workspace ONE UEM console, create a **sensor** and paste the below code into the code field.

- Name: win_office_vbamacros_status
- Run: System context

```
# This script checks the status of the vbaoff setting on Windows
# Define the registry key variables
$keyPath = "HKLM:\SOFTWARE\Policies\Microsoft\Office\16.0\Common"
$valueName = "vbaoff"

# Check if the registry key path exists
if (Test-Path $keyPath) {
# Get the value of the registry value
$value = Get-ItemProperty -Path $keyPath -Name $valueName -ErrorAction SilentlyContinue
# Check if the value is not null (i.e., the value exists)
if ($value -ne $null) {
# Check if the value is set to 1
    if ($value.$valueName -eq 1) { Write-Output "Disabled" }
    else { Write-Output "Enabled" }
}
else { Write-Output "Not set" }
}
else { Write-Output "Not set" }
```

---

## macOS configuration

### Profile
In Workspace ONE UEM console, create a **custom profile** and paste the below code into the custom settings field.

Configuration reference: https://learn.microsoft.com/en-us/deployoffice/mac/set-preference-macro-security-office-for-mac

```
<dict>
	<key>PayloadDisplayName</key>
	<string>Microsoft Office settings</string>
	<key>PayloadIdentifier</key>
	<string>com.microsoft.office.4164F689-9190-4094-A869-CE04C288947B</string>
	<key>PayloadType</key>
	<string>com.microsoft.office</string>
	<key>PayloadUUID</key>
	<string>4164F689-9190-4094-A869-CE04C288947B</string>
	<key>PayloadVersion</key>
	<integer>1</integer>
	<key>VisualBasicEntirelyDisabled</key>
	<true/>
	<key>VisualBasicMacroExecutionState</key>
	<string>DisabledWithoutWarnings</string>
</dict>
```

### Reporting
Report on Microsoft Office VBA macros status on macOS via a Bash script.

In Workspace ONE UEM console, create a **sensor** and paste the below code into the code field.

- Name: macos_office_vbamacros_status
- Language: Bash
- Response: String

```
current_user=$(ls -l /dev/console | awk '{print $3}')
vba_disabled_value=$(sudo -u $current_user defaults read "/Library/Managed Preferences/com.microsoft.office.plist" | grep "VisualBasicEntirelyDisabled" | awk '{sub(/;/, ""); print $3}')

if [ -z "$vba_disabled_value" ]; then
    echo "Not set"
elif [ "$vba_disabled_value" -eq 1 ]; then
    echo "Disabled"
else
    echo "Enabled"
fi
```