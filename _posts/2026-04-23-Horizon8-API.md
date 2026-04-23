---
layout: post
title: Automating Horizon 8 with API driven scripts
description: Practical PowerShell examples to automate common Horizon 8 tasks, including service account password rotation and operational workflows
tags: [Horizon, Script]
author: Mathieu Beaugrand
---

## Change instant clone service account password
As organisations strengthen **Active Directory hygiene** and enforce regular password rotation for service accounts, it’s important to ensure supporting platforms remain in sync. The following PowerShell script demonstrates how to automate the update of the Instant Clone service account password in **Horizon 8**, reducing manual effort and minimising the risk of authentication failures.

```powershell
[CmdletBinding(SupportsShouldProcess)]
param(
    [Parameter(Mandatory)] [securestring] $HznAdminPass,
    [Parameter(Mandatory)] [securestring] $SvcAccountPass
)

# Variables
$HznHost   = "FQDN"
$HznAdmin   = "admin"
$HznDomain = "domain"
$SvcAccount = "serviceaccount@domain.local"

# Shared request params
$restArgs = @{
    ContentType          = "application/json"
    SkipCertificateCheck = $false
}

try {
    # Login
    $body = @{
        username = $HznAdmin
        password = ConvertFrom-SecureString $HznAdminPass -AsPlainText
        domain   = $HznDomain
    } | ConvertTo-Json

    $response = Invoke-RestMethod -Method Post -Uri "https://$HznHost/rest/login" -Body $body @restArgs
    $restArgs.Headers = @{ Authorization = "Bearer $($response.access_token)" }
    Write-Host "Login successful"

    # Get IC domain account ID
    $accounts  = Invoke-RestMethod -Method Get -Uri "https://$HznHost/rest/config/v1/ic-domain-accounts" @restArgs
    $accountId = ($accounts | Where-Object { $_.username -ieq $SvcAccount }).id

    if (-not $accountId) {
        Write-Error "Service account '$SvcAccount' not found. Available accounts:"
        $accounts | Select-Object username, id | Format-Table
        exit 1
    }
    Write-Host "Account ID: $accountId"

    # Update the password
    $updateBody = @{ password = ConvertFrom-SecureString $SvcAccountPass -AsPlainText } | ConvertTo-Json

    if ($PSCmdlet.ShouldProcess("$SvcAccount on $HznHost", "Update IC domain account password")) {
        try {
            Invoke-RestMethod -Method Put -Uri "https://$HznHost/rest/config/v1/ic-domain-accounts/$accountId" -Body $updateBody @restArgs
            Write-Host "Password updated successfully for '$SvcAccount'" -ForegroundColor Green
        } catch {
            $errorDetail = $_ | ConvertFrom-Json -ErrorAction SilentlyContinue
            $errorMsg = if ($errorDetail.errors) { $errorDetail.errors[0].error_message } else { $_.Exception.Message }
            Write-Error "Failed to update password: $errorMsg"
        }
    }

} catch {
    Write-Error "Script failed: $($_.Exception.Message)"
} finally {
    # Logout - always attempt even if errors occurred
    if ($response.refresh_token) {
        try {
            $logoutBody = @{ refresh_token = $response.refresh_token } | ConvertTo-Json
            Invoke-RestMethod -Method Post -Uri "https://$HznHost/rest/logout" `
                -ContentType "application/json" `
                -Body $logoutBody
            Write-Host "Logged out successfully"
        } catch {
            Write-Warning "Logout failed: $($_.Exception.Message)"
        }
    }
}
```