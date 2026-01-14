---
layout: post
title: Bulk create EntraID users via PowerShell
subtitle: PowerShell scripts to create Entra ID users in bulk from a CSV file
tags: [EntraID, Script]
readtime: true
comments: true
author: Mathieu Beaugrand
---
Creating users in Microsoft Entra ID (formerly Azure AD) is a common task for tenant onboarding, lab builds, trials, or Proof of Concept (POC) engagements. When dealing with multiple users, automating the process can save significant time and ensure consistency. This PowerShell approach gives you flexibility to automate the user creation, licenses assignment and setup the prefered MFA method directly via Microsoft Graph.

This post walks through how to:
- Create Entra ID users in bulk from a CSV file
- Assign Microsoft licenses automatically
- Configure user's MFA method using either **Mobile Phone** or **Temporary Access Pass (TAP)**
- Reset user passwords in bulk if required

--- 

## Create a CSV file
Start by creating a CSV file that contains the users you want to create. This file will act as the input source for the scripts below. Save the file locally on your machine.

```
UserPrincipalName,FirstName,LastName,DisplayName,UsageLocation,Mail,MailNickname,Password,MobilePhone
Testuser1@beaugtech.com,Test,User 1,Test User 1,AU,Testuser1@beaugtech.com,Testuser1,Password123!,+61 123456789
Testuser2@beaugtech.com,Test,User 2,Test User 2,AU,Testuser2@beaugtech.com,Testuser2,Password123!,+61 123456789
```

{: .box-note}
**Tip:** Ensure each column header exactly matches the script variable names. For production use, avoid storing plaintext passwords in CSV files. Consider using Temporary Access Pass (TAP) or prompting users to set their own password.

---

## Bulk create users with mobile phone MFA - Option 1
The following script:
- Connects to Microsoft Graph
- Creates users from the CSV
- Assigns a Dev E5 license
- Adds the mobile phone number as an MFA method

```powershell
Connect-MgGraph -Scopes `
    "User.ReadWrite.All",
    "UserAuthenticationMethod.ReadWrite.All"

# Import CSV
$users = Import-Csv -Path "~/Downloads/EntraID-User-Create-Bulk.csv"

# Get Microsoft License SKU
$e5Sku = Get-MgSubscribedSku -All |
    Where-Object SkuPartNumber -eq 'DEVELOPERPACK_E5'

foreach ($user in $users) {

    # Password profile from CSV
    $passwordProfile = @{
        ForceChangePasswordNextSignIn = $false
        Password = $user.Password
    }

    try {
        # Create user
        $newUser = New-MgUser `
            -DisplayName $user.DisplayName `
            -GivenName $user.FirstName `
            -Surname $user.LastName `
            -UserPrincipalName $user.UserPrincipalName `
            -UsageLocation $user.UsageLocation `
            -Mail $user.Mail `
            -MailNickname $user.MailNickname `
            -MobilePhone $user.MobilePhone `
            -PasswordProfile $passwordProfile `
            -AccountEnabled:$true

        Write-Host "User created: $($user.UserPrincipalName)" -ForegroundColor Green

        # Assign license
        Set-MgUserLicense `
            -UserId $newUser.Id `
            -AddLicenses @{ SkuId = $e5Sku.SkuId } `
            -RemoveLicenses @()

        # Give Entra time to finalize user provisioning
        Start-Sleep -Seconds 15

        # Add MFA mobile phone method
        if ($user.MobilePhone) {
            New-MgUserAuthenticationPhoneMethod `
                -UserId $newUser.Id `
                -PhoneNumber $user.MobilePhone `
                -PhoneType "Mobile"

            Write-Host "MFA mobile phone added: $($user.MobilePhone)" -ForegroundColor Yellow
        }
        else {
            Write-Host "No mobile phone provided - MFA skipped" -ForegroundColor DarkYellow
        }
    }
    catch {
        Write-Host "Failed for $($user.UserPrincipalName): $_" -ForegroundColor Red
    }
}
```

## Bulk create users with Temporary Access Pass (TAP) - Option 2
If you prefer using Temporary Access Pass (TAP) instead of mobile phone MFA, the script below creates users and generates a TAP for each one. The TAP values are exported to a CSV file for secure distribution.

```powershell
Connect-MgGraph -Scopes `
    "User.ReadWrite.All",
    "UserAuthenticationMethod.ReadWrite.All"

# Import users
$users = Import-Csv -Path "~/Downloads/EntraID-User-Create-Bulk.csv"

# Output file
$outputPath = "~/Downloads/EntraID-User-TAP-Export.csv"
$tapResults = @()

# Get Microsoft License SKU
$e5Sku = Get-MgSubscribedSku -All |
    Where-Object SkuPartNumber -eq 'DEVELOPERPACK_E5'

foreach ($user in $users) {

    $passwordProfile = @{
        ForceChangePasswordNextSignIn = $false
        Password = $user.Password
    }

    try {
        # Create user
        $newUser = New-MgUser `
            -DisplayName $user.DisplayName `
            -GivenName $user.FirstName `
            -Surname $user.LastName `
            -UserPrincipalName $user.UserPrincipalName `
            -UsageLocation $user.UsageLocation `
            -Mail $user.Mail `
            -MailNickname $user.MailNickname `
            -MobilePhone $user.MobilePhone `
            -PasswordProfile $passwordProfile `
            -AccountEnabled:$true

        # Assign license
        Set-MgUserLicense `
            -UserId $newUser.Id `
            -AddLicenses @{ SkuId = $e5Sku.SkuId } `
            -RemoveLicenses @()
        
        # Give Entra time to finalize user provisioning
        Start-Sleep -Seconds 15

        # Create Temporary Access Pass (TAP) for MFA
        $tap = New-MgUserAuthenticationTemporaryAccessPassMethod `
            -UserId $newUser.Id `
            -TemporaryAccessPass @{
                IsUsableOnce        = $false       # Allow reuse
                LifetimeInMinutes  = 480           # 8 hours expiry
            }

        # Capture results
        $tapResults += [PSCustomObject]@{
            UserPrincipalName = $user.UserPrincipalName
            TemporaryAccessPass = $tap.TemporaryAccessPass
            ExpiresInMinutes = 480
        }

        Write-Host "User created + TAP issued: $($user.UserPrincipalName)" -ForegroundColor Green
    }
    catch {
        Write-Host "Failed for $($user.UserPrincipalName): $_" -ForegroundColor Red
    }
}

# Export TAPs
$tapResults | Export-Csv -Path $outputPath -NoTypeInformation -Force

Write-Host "`nTAP export completed:" -ForegroundColor Cyan
Write-Host $outputPath -ForegroundColor Yellow
```

---

## Bulk password reset
Occasionally, you may need to reset user passwords, the following script updates user passwords in bulk using the same CSV file.

```powershell
# Connect to Microsoft Graph with required permissions
Connect-MgGraph -Scopes `
    "Directory.AccessAsUser.All",
    "User.ReadWrite.All"

# Import the CSV file (ensure it has a column for UserPrincipalName or Id)
$users = Import-Csv -Path "~/Downloads/EntraID-User-Create-Bulk.csv"

foreach ($user in $users) {
    # Set new password profile
    $PasswordProfile = @{
        ForceChangePasswordNextSignIn = $false
        Password = $user.Password
    }

    # Update the user's password
    Update-MgUser -UserId $user.UserPrincipalName -BodyParameter @{
        passwordProfile = $PasswordProfile
    }

    Write-Host "Updated password for user:" $user.UserPrincipalName
}
```