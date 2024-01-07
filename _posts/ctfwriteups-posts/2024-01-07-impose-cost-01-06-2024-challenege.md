---
layout: post
title: Impose Cost 01-06-2024 Challenge
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
tags: [imposecost,ctf-decoding]
after-content: [disclaimer-notice.html]
---
### Explanation
On January 6, 2024, [@ImposeCost](https://twitter.com/ImposeCost/status/1743691690588647491) posted a challege to decode text and obtain the "flag".

<img src="{{ 'assets/img/2024-01-07-impose-cost-01-06-2024-challenege/2024-01-07-impose-cost-01-06-2024-challenege-screenshot.png' | relative_url }}" alt='Impose Cost 01-06-24 Challenge Screenshot' />

### Solving the Challenge
They way I solved this challenge is *not* the intended method. I missed an important step but I was still able to solve the challenge. Hopefully this helps someone else.

1. Looking at the encoded text, it appears to be hexadecimal. The characters are "a" through "f", "A" through "F", and "0" through "9". Let's use [CyberChef](https://gchq.github.io/CyberChef/) to start decoding the text, use "From Hex" and paste the encoded text in the Input box.

<img src="{{ 'assets/img/2024-01-07-impose-cost-01-06-2024-challenege/2024-01-07-impose-cost-01-06-2024-challenege-from-hex-screenshot.png' | relative_url }}" alt='Impose Cost 01-06-24 Challenge from hex.png' />

2. Looking at the Ouput, the text appears to be base64. Something that gives us a clue that it is base64 is the use of the "=" for padding at the end of the string.

<img src="{{ 'assets/img/2024-01-07-impose-cost-01-06-2024-challenege/2024-01-07-impose-cost-01-06-2024-challenege-base64-screenshot.png' | relative_url }}" alt='Impose Cost 01-06-24 Challenge base64.png' />

I added the "From Base64" Operation and that shows up new output.

<img src="{{ 'assets/img/2024-01-07-impose-cost-01-06-2024-challenege/2024-01-07-impose-cost-01-06-2024-challenege-from-base64-screenshot.png' | relative_url }}" alt='Impose Cost 01-06-24 Challenge from base64.png' />

We need to determine what type of file this is. You can do that by checking the header. This header might not be easy to quickly identify if you haven't encountered this type of file in the past. CyberChef offers multiple ways to determine what type of file this is. One method is to use the "Magic" Operation which will take the output and show you multiple possible new outputs and which Operations will provide each result. Another method is to click the "Magic Wand" button. Both methods show the file is likely compressed using Gzip.

3. Use the Gunzip Operation

<img src="{{ 'assets/img/2024-01-07-impose-cost-01-06-2024-challenege/2024-01-07-impose-cost-01-06-2024-challenege-gunzip-screenshot.png' | relative_url }}" alt='Impose Cost 01-06-24 Challenge gunzip.png' />

4. You might recognize the value as Base32 or you can use the "Magic" Operation or click the "Magic Wand" button.


