---
layout: post
title: Get-PKICertificates
gh-repo: 4D5A/Systems-Administration
gh-badge: [follow, star]
categories: [projects]
tags: [DNS, Systems-Administration-Tools]
after-content: [disclaimer-notice.html]
---
### Introduction
When I first started scripting with PowerShell, I came across the Get-PSDrive cmdlet. [https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-psdrive?view=powershell-7.3](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-psdrive?view=powershell-7.3) Immediately one of the things that stood out to me was that with PowerShell, it was possible to access storage devices, the registry, and certificate stores similar to the process of accessing storage devices with Windows Command Prompt. One of the PowerShell scripts I wrote allowed me to delete or add a certificate to the certificate store. Years later, a post by [@mubix@infosec.exchange](https://infosec.exchange/@mubix), helped me think of a possible use case for such a script. Accoring to [@mubix@infosec.exchange](https://infosec.exchange/@mubix),<sup>1</sup> there were pentesters who in some cases need to install certificates during their work but don't remember to delete them later which can create a security vulnerability. He suggested that Network Defenders require thumbprints for all certificates the pentester(s) created so the Network Defenders can delete them.

### Accessing Windows' Certificate Stores in PowerShell


### Footnotes
[1] [https://infosec.exchange/@mubix/109379491407168790](https://infosec.exchange/@mubix/109379491407168790)