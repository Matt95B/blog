---
layout: post
title: List biometric devices on Windows via PowerShell
subtitle: Report on available biometric devices present on Windows via a PowerShell script.
tags: [Windows, Script]
readtime: true
comments: true
author: Mathieu Beaugrand
---

Run: System context

```powershell
$biometricDevices = Get-CimInstance -ClassName Win32_PnPEntity | Where-Object { $_.PNPClass -eq 'Biometric' -and $_.Present }

$devicesArray = New-Object System.Collections.ArrayList

if ($biometricDevices) {
    foreach ($device in $biometricDevices) {
        [void]$devicesArray.Add($device.Description)
    }
    $output = $devicesArray -join ","
    Write-Output $output
} else {
    Write-Output "No biometric devices"
}
```