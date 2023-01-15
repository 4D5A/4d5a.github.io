---
layout: post
title: Custom Windows Hardening
gh-repo: 4D5A/windows_hardening
gh-badge: [follow, star]
categories: [projects]
tags: [Systems-Administration-Tools, infosec]
after-content: [disclaimer-notice.html]
---
## Introduction

[4D5A/windows_hardening](https://github.com/4D5A/windows_hardening) is a personal fork of [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) by Michael Schneider [0x6d69636b](https://github.com/0x6d69636b). [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) is a repository used for development and the production repository is [HardeningKitty](https://github.com/scipag/HardeningKitty) by [scipag](https://github.com/scipag). The PowerShell module in the [scipag/HardeningKitty](https://github.com/scipag/HardeningKitty) repository is digitally signed by scipag.<sup>1</sup>

I am not employed by or otherwise affiliated with [0x6d69636b](https://github.com/0x6d69636b) or [scip ag](https://github.com/scipag) other than occassionally contributing to the [0x6d69636b/windows_hardening](https://github.com/0x6d69636b/windows_hardening) repository.

## Overview

Several paid tools offer the ability to scan computers' settings to determine the conformance with security standards such as CIS Benchmarks. I have not found many open source or freeware solutions that allow you to scan computers' settings to determine their conformance with those security standards let alone apply the settings for a standard. 0x6d69636b's windows_hardening solution is such a tool. It is free and open source. It gives infosec professionals the ability to evaluate a computer's configuration to assess its conformance with a specific checklist that is written as a CSV file. 0x6d69636b maintains checklists related to Microsoft, CIS Benchmarks, and other security standards. 0x6d69636b also maintains a custom checklist.

I have also created a custom checklist which I continue to modify.

windows_hardening can be used to check the settings of a computer and to apply settings contained in a checklist. If no checklist is specified, 0x6d69636b's custom checklist is used.

## Install windows_hardening

You can install windows_hardening by downloading the newest release, copying the files, and importing the PowerShell module.

Alternatively, you can run the PowerShell function below which automatically downloads the newest release from the [4D5A/windows_hardening](https://github.com/4D5A/windows_hardening) repository.

```powershell
Function InstallWindowsHardening() {
    $Version = ((Invoke-WebRequest "https://api.github.com/repos/4d5a/windows_hardening/releases/latest" -UseBasicParsing) | ConvertFrom-Json).Name
    $WindowsHardeningLatestVersionDownloadLink = ((Invoke-WebRequest "https://api.github.com/repos/4d5a/windows_hardening/releases/latest" -UseBasicParsing) | ConvertFrom-Json).zipball_url
    $ProgressPreference = 'SilentlyContinue'
    Invoke-WebRequest $WindowsHardeningLatestVersionDownloadLink -Out WindowsHardening$Version.zip
    Expand-Archive -Path ".\WindowsHardening$Version.zip" -Destination ".\WindowsHardening$Version" -Force
    $Folder = Get-ChildItem .\WindowsHardening$Version | Select-Object Name -ExpandProperty Name
    Move-Item ".\WindowsHardening$Version\$Folder\*" ".\WindowsHardening$Version\"
    Remove-Item ".\WindowsHardening$Version\$Folder\"
    New-Item -Path $Env:ProgramFiles\WindowsPowerShell\Modules\WindowsHardening\$Version -ItemType Directory
    Set-Location .\WindowsHardening$Version
    Copy-Item -Path .\WindowsHardening.psd1,.\WindowsHardening.psm1,.\lists\ -Destination $Env:ProgramFiles\WindowsPowerShell\Modules\WindowsHardening\$Version\ -Recurse
    Import-Module "$Env:ProgramFiles\WindowsPowerShell\Modules\WindowsHardening\$Version\WindowsHardening.psm1"
}
InstallWindowsHardening
```

## Using windows_hardening

### Config
```powershell
Invoke-HardeningKitty -Mode Config -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```
### Audit
```powershell
Invoke-HardeningKitty -Mode Audit -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```

### HailMary
```powershell
Invoke-HardeningKitty -Mode HailMary -FileFindingList .\lists\finding_list_0x6d69636b_user.csv -Log -Report
```

[1] [https://github.com/scipag/HardeningKitty](https://github.com/scipag/HardeningKitty)
