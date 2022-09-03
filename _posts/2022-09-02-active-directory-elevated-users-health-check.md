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

After discussing the report with others, and I questioned if the tool was identifying which accounts had elevated privileges by looking at the value of the adminCount attribute on Active Directory User Objects.

When an Active Directory Object is added to Protected Active Directory group<sup>1</sup> (these are different from the Protected Users Security Group), a few things take place. One of the updates to the Active Directory Object that is a member of a protected Active Directory group, is the value of the object's adminCount attribute is updated from ```<NOT SET>``` or ```($null)``` to ```1```.<sup>2</sup> A value of "1" for the adminCount attribute, indicates the Active Directory Object currently or previously had elevated privileges as a reulst of being a member of a protected Active Directory group.

> If you remove an Active Directory object from all protected Active Directory groups, SDProp does not reset the value of the adminCount Active Directory attribute to "0" or <NOT SET>




 permissions of the protected Active Directory group are compared with the permissions of the AdminSDHolder object in Active Directory, if the permissions of the protected Active Directory group are different than those of the AdminSDHold object are different, they are changed to the permissions of the AdminSDHolder object. 



the value of adminCount  The element of Active Directory that is responsible for filtering through Active Directory Objects to see if they are a member of a Protected Group Active Directory Security Group and updating the value of adminCount to "1" is named the "Security Descriptor Propogator." When an Active Directory Object is removed from such an Active Security Group, Security Descriptor Propogator (SDPROP) does not automatically change the value of adminCount from "1" to "0" or $null.




[1] [https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/](https://devblogs.microsoft.com/scripting/powertip-use-powershell-to-find-if-user-is-nested-group-member/)

[2] [https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx](https://social.technet.microsoft.com/wiki/contents/articles/22331.adminsdholder-protected-groups-and-security-descriptor-propagator.aspx)