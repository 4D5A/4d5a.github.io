---
layout: post
title: Invoke-DNSQuery
gh-repo: 4D5A/Networks-Administration
gh-badge: [follow, star]
categories: [projects]
tags: [DNS, Network-Administration-Tools]
after-content: [disclaimer-notice.html]
---
### Introduction
Many Network Administrators and Systems Administrators frequently respond to questions about why an email wasn't received by someone in their organization. The questions are often asked with a tone of "why did you block my email?" or "something is obviously wrong, because I should have received that email" and the people asking sometimes come off as accusing the Network Administrator or Systems Administrator of doing something wrong. Many times, emails are blocked by spam filters because they failed one or more processes to verify the email was sent from an authorized server for the sending domain name and the content of the email did not change from the time it was sent until the time it was received. The processes verify that information are named Sender Policy Framework (SPF), DomainKeys Identified Mail (DKIM), and Domain-based Message Authentication Reporting & Conformance (DMARC).    

If SPF and DKIM are not familiar to you, the resources below provide more details for each.

- [Configuring sender policy framework in Exchange Online](https://4d5a.github.io/2022-04-23-configuring-sender-policy-framework-in-exchange-online/)
- [How to configure DKIM in Microsoft 365](https://4d5a.github.io/2022-04-23-how-to-configure-dkim-in-microsoft-365/)

For a technical description of SPF including a discussion of RFC 7208, you may consider reviewing [Blocking spoofed emails](https://4d5a.github.io/2021-12-11-blocking-spoofed-emails/).

### Manually reviewing SPF, DKIM, and DMARC records
In the past, when someone asked Network Administrators and Systems Administrators about an email which was blocked, the Network Administrator or Systems Administrator would review the email filter's logs and often use a website like [mxtoolbox.com](https://www.mxtoolbox.com) to check for problems with the sending domain's SPF, DKIM, and DMARC records. Given the frequency of being asked this type of question, I wanted a PowerShell script that could check the public DNS information of the sending domain to see if there were SPF or DMARC misconfigurations. Specifically, I wanted the script to query public DNS for the sending domain's SPF and DMARC records, identify if there were duplicate records, identify if those records were missing, the SPF qualifier, the DMARC mode, and to attempt to determine the email or filter provider for the queried domain name. I also wanted to be able to query multiple domain names at once and export the results to a csv file.

#### Using MX records to determine the email or filter provider

Here is a table of strings which may be located in MX records and matching email or filter providers. This information is subject to change and must not be considered to be exhaustive. You should refer to current documentation provided by each email and filter provider for what MX records they each require.

| olc.protection.outlook.com | Outlook.com |
| mail.protection.outlook.com | Exchange Online |
| mimecast.com | Mimecast |
| pphosted.com | Proofpoint |
| hydra.sophos.com | Sophos |
| barracudanetworks.com | Barracuda Networks |
| secureserver.net | GoDaddy |
| aspmx.l.google.com | Google |

PowerShell includes the ```Resolve-DnsName``` cmdlet which provides you with the ability to query DNS records. Unlike the output obtanied from ```nslookup```, ```Resolve-DnsName``` produces object-oriented output, which in turn makes assigning a specific portion of a DNS query result as a variable. Microsoft's documentation for the ```Resolve-DnsName``` cmdlet is accessible at [https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname](https://learn.microsoft.com/en-us/powershell/module/dnsclient/resolve-dnsname).

An example of using the ```Resolve-DnsName``` cmdlet might be querying Google's Public DNS server 8.8.8.8 for the MX records of google.com.

```Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd```

The example query returns the below object.

| Name | Type | TTL | Section | NameExchange | Preference |
| google.com | MX | 201 | Answer | smtp.google.com | 10 |

Since the ```Resolve-DnsName``` cmdlet is object-oriented, assigning the output of the query to a variable is staightforward.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd```

Now that I assigned a variable to the object, I can use the usual PowerShell commands to include Select-Object and Where-Object to get the specific object properties and assign a variable to them.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd | Select-Object NameExchange -ExpandProperty```

If you copy and paste the code above in PowerShell and then type ```$MailExchangeRecord```, you will receive the below output.

```smtp.google.com```

Since most domain names have multiple DNS records for failover, I decided to focus on the primary MX record for the domain name by sorting the output by the object property ```Preference``` in descending order and then using Select-Object to select the last entry which will produce the MX record for the domain name with the object property ```Preference``` that has the lowest value.

```$DNSRecord = Resolve-DnsName -Name google.com -Type MX -Server 8.8.8.8 -DnssecCd | Sort-Object -Property Preference -Descending | Select-Object -Last 1 | Select-Object -ExpandProperty NameExchange```

#### Using TXT records to detect missing or misconfigured SPF and DMARC records
There are common misconfigurations of SPF and DMARC records which we can use to TXT records to test for.

##### Using TXT records to detect missing or misconfigured SPF records
Two common SPF record problems are there not being one or there being more than one. If the domain name does not have an SPF record, destination spam filters may block emails from that domain name. If the domain name has multiple SPF records, when the destination spam filter receives the email from that domain name, the destination spam filter will request the SPF record for the domain name and the DNS server will provide the value of one of the SPF records. If those records are different (and one of the records is wrong), some emails from the sending domain will pass the SPF check of the spam filter and others will fail. This presents itself as emails from a given domain name sporadically being blocked.

We are able to use ```Resolve-DNSName``` to obtain SPF records for a domain name.

```$SPFDetails = Resolve-DnsName -Name $DomainName -Type TXT -Server $DnsIp -DnssecCd | Where-Object -Property Strings -match -Value "spf1"```

I could use an ```If``` statement like ```If (-Not($SPFDetails))``` with the example variable above to check if a domain name does not have an SPF record, but because I also plan on checking if there are multiple SPF records, I decided to count the number of SPF records.

```$SPFRecordCount = (Resolve-DnsName -Name $DomainName -Type TXT -Server $DnsIp -DnssecCd | Where-Object -Property Strings -match -Value "spf1").count```

This checks if the domain name does not have an SPF record.

<pre> If ($SPFRecordCount -eq 0) {
        $SPFRecord = "MISCONFIGURATION: No SPF record."
    }</pre>


### Footnotes
