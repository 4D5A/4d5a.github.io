---
layout: post
title: Active Directory Elevated Users Health Check
gh-repo: 4D5A/Active-Directory-Tools
gh-badge: [follow, star]
categories: [blog]
tags: [Active Directory]
after-content: [disclaimer-notice.html]
---
### Introduction
I reviewed the configuration of my domain controller and as part of my review, I used a security audit program to review the security configuration of my domain. It found several users which it stated had elevated privileges. I reviewed their Active Directory group membership but they only appeared to be members of Domain Users. I was confused how they could have elevated privileges if they were only members of the Domain Users security group. One possible answer is if Domain Users was added as a member of a privileged group. I checked but Domain Users was not a member of another group. It seemed that the users did not have elevated privileges. That left me with the question, "Why did the tool believe the users had elevated privileges?"

### How does the Active Directory Object Attribute adminCount Work?
After discussing the report with others, and I questioned if the tool was identifying which accounts had elevated privileges by looking at the value of the adminCount attribute on Active Directory User Objects.

When an Active Directory Object is added to Protected Active Directory group<sup>1</sup> (these are different from the Protected Users Security Group), the Security Descriptor Propogator (SDProp), evalutes a number of pieces of metadata associated with the Active Directory object that was added to a Protected Active Directory group. One of its tasks is ensure that the value of the object's adminCount attribute is updated from ```<NOT SET>``` or ```0``` to ```1```.<sup>2</sup> A value of ```1``` for the adminCount attribute, indicates the Active Directory Object currently or previously had elevated privileges as a reulst of being a member of a protected Active Directory group.

> If you remove an Active Directory object from all protected Active Directory groups, SDProp does not reset the value of the adminCount Active Directory attribute to ```0``` or ```<NOT SET>```.<sup>3</sup>

This can lead to reports created by automated security tools identifying Active Directory objects as having elevated privileges when they actually do not. If an Active Directory object was previously added to the "Domain Admins" security group, but was later removed from that security group, it would still have an adminCount attribute value of ```1``` because SDProp does not reset the value of the adminCount attribute when an Active Directory object is removed from a protected group.

### What is the Protected Users group?
If an Active Directory user object is an elevated user and that is what you need it to be, you should consider adding it to the Protected Users group if it isn't. Users that are a member of the Protected Users group receive additional security protections including:

* Credential delegation does not cache the user's credentials in plain text
* Windows Digest does not cache the user's credentials in plain text
* NTLM does not cache the user's credentials in plain text
* Kerberos will not create DES or RC4 keys or cache the user's credentials in plain text

The result is users that are a member of the Protected Users group cannot do the following:

* Authenticate using NTLM
* Authenticate using DES or RC4 through Kerberos
* Renew Kerberos Ticket Granting Tickets for more than the original 4 hours

> Do not enable Protected Users if you have any Windows Server 2003 Domain Contollers in your domain<sup>4</sup>

> To use Protected Users, you need to have your Primary Domain Controller Emulator Role running on a server that is Windows Server 2012 R2 or higher<sup>5</sup>

### Building the Get-ElevatedADUsers Project

## Project Objectives
My objective of this project were:

* Identify the Active Directory user objects that have an adminCount attribute other than ```0``` or ```<NOT SET>```
* Identify the Active Directory user objects that are currently a member of a Protected Group
* Identify the Protected Groups the Active Directory user object is a direct member of
* Identify the Protected Groups the Active Directory user object is a transitive member of
* Identify if the user object is a member of the Protected Users group

## Get-ElevatedADUsers.ps1
This script is written in PowerShell and requires the ActiveDirectory module. It is designed to be run as a elevated user on a Domain Controller.

The script has two optional parameters.

1. ```$ReportLocation``` - If this parameter is not specified, the value for ```$ReportLocation``` will be set to ```"$env:USERPROFILE\Desktop\"```.
2. ```$File``` - If this paramter is not specified, the value for ```$File``` will be set to ```"Get-ElevatedADUsers_Report_$(Get-Date -Format ddMMyyyy_HHMMss).txt"```.

The two optional switches are:

1. ```-IncludeDisabled``` - This sets the variable ```$ElevatedUsers``` to ```"Get-ADUser -Filter {(adminCount -ne 0)}"```.
2. ```-LookCool``` - I added this becase the switch ```-Verbose``` is reserved by PowerShell v2.








[1] [https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/)

[2] [https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx](https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx)

[3] [2022-09-02-active-directory-elevated-users-health-check](2022-09-02-active-directory-elevated-users-health-check)

[4] [https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq)

[5] [https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts#BKMK_Prereq)


https://github.com/4D5A/Active-Directory-Tools/blob/main/Security%20Tools/Get-ElevatedADUsers.ps1