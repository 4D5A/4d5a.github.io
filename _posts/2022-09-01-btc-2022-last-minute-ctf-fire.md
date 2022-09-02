---
layout: post
title: Blue Team Con 2022 Last Minute CTF - Fire
gh-repo: 4D5A
gh-badge: [follow]
categories: [ctfwriteups]
tags: [BlueTeamCon2022,ctf-forensics]
after-content: [disclaimer-notice.html]
---

## Rhymes

### Explanation
In the Rhymes Challenge, you are provided with a PDF. It contains two lines of redacted text. The first line of redacted text is on page 2. The second line of redacted text is on line 4.

There are several ways you can uncover the "redacted" text in the PDF. This demonstrates the problems with highlighting in a dark color as a form of "redacting". Someone else can highlight the "redacted" text, copy it, and paste it into a different program like a text editor. Because the original text was not replaced but was only covered with what is essentially black highlighting which is added as a new layer, it can be copy and pasted into a different document.

If you got the flag BTC[Mots D'Heures: Grousses, Rames] and did not receive points for it, that is because it is not the flag. The Rules at http://18.205.188.77/rules tell us that flags will use the format, ```BTC{Flag_goes_here}```.

If you go to page 4, there is a second line of "redacted" text which contains the actual flag.


### Solving the Challenge
1. Run the command mkdir -p ~/CTF/Fire
2. Browse to http://18.205.188.77/challenges.
3. Click Fire.
4. Click key4.db and logins.json to download the files. Save the files in ~/CTF/Fire
5. Run the command cd ~/CTF/Fire
6. Run the command git clone https://github.com/lclevy/firepwd.git
7. Run the command cd firepwd
8. Run the command pip install -r requirements.txt
9. Run the command python3 ./firepwd.py -d ~/CTF/Fire
10. The flag is BTC{key3.dbPassword}
11. Copy the flag
12. Paste the flag into the Flag field.
13. Click Submit.

<img src="{{ 'assets/img/2022-09-01-btc-2022-last-minute-ctf-fire/btc-2022-last-minute-ctf-fire-screenshot.png' | relative_url }}" alt='BTC 2022 Last Minute CTF Fire Screenshot' />