# 🔍 Footprinting & Network Scanning

**Reconnaissance of an authorized web target with Kali Linux tools, and host discovery on my own LAN with Zenmap**

![Skill](https://img.shields.io/badge/Skill-Cybersecurity-404040?style=flat-square&labelColor=C00000) ![Phase](https://img.shields.io/badge/Phase-Reconnaissance%20%26%20Scanning-0070C0?style=flat-square&labelColor=000000) ![Kali Linux](https://img.shields.io/badge/Platform-Kali%20Linux-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white) ![Nmap](https://img.shields.io/badge/Tool-Zenmap%20%2F%20Nmap-238F89?style=flat-square&labelColor=000000) ![Skill](https://img.shields.io/badge/Skill-Footprinting-404040?style=flat-square&labelColor=C00000) ![Skill](https://img.shields.io/badge/Skill-DNS%20Enumeration-404040?style=flat-square&labelColor=C00000) ![Penetration Testing](https://img.shields.io/badge/Penetration%20Testing-C00000?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white) ![GitHub](https://img.shields.io/badge/GitHub-404040?style=flat-square&labelColor=0070C0&logo=github&logoColor=white) ![NetworkWalks](https://img.shields.io/badge/NetworkWalks-404040?style=flat-square&labelColor=C00000) ![Ethical Hacking](https://img.shields.io/badge/Ethical%20Hacking-E87500?style=flat-square&labelColor=000000) ![Author](https://img.shields.io/badge/Mubassir-C00000?style=flat-square)

---

## 📌 Project Overview

This is my **Week 2 – Project Modules 1 and 5 (W2-PM1 and W2-PM5)** submission for the Networkwalks Cybersecurity Internship, Batch **B084K**.

The project covers the first two phases of a penetration test:

- **Phase 1: Reconnaissance & Footprinting.** Six Kali Linux tools were used to collect public information about an authorized web target.
- **Phase 2: Scanning & Network Discovery.** Zenmap was used to find live hosts on my own local network.

Together they show how an attacker moves from gathering public information to mapping live hosts.

> **Note:** All testing was done under a written **Letter of Authorization** (valid 12 to 19 November 2026). The web target was limited to the client's own public website and DNS records, and the network scan was limited to my own LAN. Nothing outside this scope was tested.

---

## 🎯 Objectives

- Collect domain registration details with WHOIS.
- Fingerprint web technologies with WhatWeb.
- Resolve the domain to an IP address with Nslookup.
- Inspect HTTP response headers with Curl.
- Detect a Web Application Firewall with Wafw00f.
- Enumerate DNS, mail and SPF records with DNSRecon.
- Identify my local IP, subnet and gateway with `ipconfig`.
- Discover live hosts on my LAN with a Zenmap ping scan.
- View and save the network topology.
- Rate each finding by risk level and write recommendations.

---

## 🛡️ Scope & Ethical Use

⚠️ **Important:** Reconnaissance and scanning were performed only on systems where I had written permission or that I own.

| ✅ In scope | ❌ Not performed |
|---|---|
| The client's public website and its DNS records | Exploitation or gaining access |
| My own local network | Denial-of-service or brute-force attacks |
| Passive and light information gathering | Accessing, copying or changing any data |
| Ping scan (host discovery) | Port scans or vulnerability scans |

In most countries, unauthorised access is a crime even when nothing is damaged. Never use these tools against systems you do not own or have written permission to test.

---

## 🧰 Tools Used

| 🧩 Tool | 🖥️ Command | 🎯 Purpose |
|---|---|---|
| WHOIS | `whois <domain> \| tee whois.txt` | Registration details, name servers, dates |
| WhatWeb | `whatweb --color=never <domain> \| tee whatweb.txt` | Web technology fingerprinting |
| Nslookup | `nslookup <domain> \| tee nslookup.txt` | Resolve the domain to an IP address |
| Curl | `curl -I https://<domain> \| tee curl.txt` | Read HTTP response headers |
| Wafw00f | `wafw00f <domain> \| tee wafw00f.txt` | Detect a Web Application Firewall |
| DNSRecon | `dnsrecon -d <domain> \| tee dnsrecon.txt` | Enumerate NS, MX, SPF, TXT and SRV records |
| Windows CMD | `ipconfig` | Local IP, subnet mask and gateway |
| Zenmap (Nmap GUI) | `nmap -sn <subnet>/24` | Ping scan to find live hosts |

> Each command output was saved to a text file with `tee`, so there is a record of every result.

---

# 🪜 Phase 1: Footprinting & Reconnaissance

All Phase 1 commands were run in Kali Linux inside my lab from Week 1.

## Step 1. WHOIS

WHOIS returned the domain registration record: the registrar, creation and expiry dates, domain status locks, and the name servers. The name servers show which company hosts the site. The owner's details are hidden behind a privacy service, and **DNSSEC is shown as unsigned**.

![whois output](screenshots/01-whois.png)

## Step 2. WhatWeb

WhatWeb fingerprinted the website and identified the CMS, a download-manager plugin, the web server and a JavaScript library, along with their version numbers. It also showed the HTTP to HTTPS redirect.

![whatweb output](screenshots/02-whatweb.png)

## Step 3. Nslookup

Nslookup (using the public DNS server `8.8.8.8`) resolved the domain name to the public IP address of the web server.

![nslookup output](screenshots/03-nslookup.png)

## Step 4. Curl

`curl -I` returned `HTTP/1.1 200 OK` with a `Server` banner, a cache header, a secure `HttpOnly` cookie, and a `Link` header that points to the WordPress REST API.

![curl output](screenshots/04-curl.png)

## Step 5. Wafw00f

Wafw00f detected that the site is behind a **ModSecurity (SpiderLabs)** Web Application Firewall.

![wafw00f output](screenshots/05-wafw00f.png)

## Step 6. DNSRecon

DNSRecon enumerated the DNS records and showed:

- DNSSEC is **not configured**
- SOA and name server records at the hosting provider
- A mail server record
- An SPF record ending in `~all` (soft fail)
- A Google site-verification TXT record
- 8 cPanel `_autodiscover._tcp` SRV records

![dnsrecon output](screenshots/06-dnsrecon.png)

---

# 🪜 Phase 2: Network Scanning with Zenmap

All Phase 2 work was done on my own Windows PC and my own LAN.

## Step 7. Identify the local network

`ipconfig` showed my Wi-Fi adapter's IPv4 address, subnet mask (`255.255.255.0`) and default gateway. This gave me my LAN subnet in CIDR form, `/24`, which is 256 addresses. The VMware virtual adapters (VMnet1 and VMnet8) were **not** scanned.

![ipconfig output](screenshots/07-ipconfig.png)

## Step 8. Ping scan

In Zenmap, I entered my subnet as the target and ran a ping scan:

```bash
nmap -sn <my-subnet>/24
```

The scan used Nmap 7.98, covered **256 IP addresses**, and found **2 live hosts** in about 10 seconds.

| # | Host | MAC address | Note |
|---|---|---|---|
| 1 | My own PC (Wi-Fi adapter) | Not shown by Nmap | The machine running the scan |
| 2 | Default gateway | Vendor unknown | Locally administered address |

> Nmap does not report the MAC address of the machine it runs on. My own MAC address comes from `ipconfig /all` instead.

![Zenmap ping scan](screenshots/08-zenmap-scan.png)

## Step 9. Network topology

The **Topology** tab shows my machine (`localhost`) in the centre, connected to the two live hosts. I enabled the legend and saved the topology as a PDF, as the practical task required.

![Zenmap topology](screenshots/09-topology.png)

![Zenmap topology with legend](screenshots/10-topology-legend.png)

---

# ⚠️ Risk Analysis

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
|---|---|---|---|---|
| 1 | Web technology and version information exposed | WhatWeb identified the CMS, a plugin, the web server and a JS library with versions | Exposed versions help an attacker look up known issues, so these components need a security review | 🟠 Medium |
| 2 | Server IP address identifiable | Nslookup resolved the domain to a public IP | Shows the network location of the web service | 🟢 Low |
| 3 | HTTP technical information exposed | Curl showed a server banner, a cache header and the REST API link | May assist fingerprinting and further enumeration | 🟢 Low |
| 4 | WAF technology identifiable | Wafw00f identified ModSecurity (SpiderLabs) | Reveals information about the security architecture | 🟢 Low |
| 5 | DNS information exposed; DNSSEC not configured | DNSRecon showed hosting name servers, a mail host, SPF ending in `~all` and cPanel SRV records | Helps build an infrastructure profile; a soft-fail SPF is weaker than a strict policy against email spoofing | 🟠 Medium |
| 6 | Hosting provider identifiable from WHOIS | Name servers point to the host; registrar is visible; owner details are hidden by a privacy service | Reveals hosting and registrar details, although owner details are protected | 🟢 Low |
| 7 | Live hosts visible on local network | Zenmap found 2 live hosts, both accounted for (my PC and the gateway) | No unknown devices were seen | 🟢 Low |

**Risk level key:** 🔴 Critical · 🟠 Medium · 🟢 Low

> These are **observations, not confirmed vulnerabilities**. This project involved only information gathering and host discovery. A software version, IP address or DNS record does not by itself mean a system is vulnerable. Further authorized testing would be needed to confirm any real weakness. Nothing was rated Critical for this reason.

---

# ✅ Recommendations

1. **Review publicly exposed technology information.** Regularly check what the website reveals about its CMS, plugins and versions.
2. **Keep software updated.** Update WordPress and its plugins, and check them against current security advisories.
3. **Review HTTP headers.** Remove unnecessary server and cache banners, and review which REST API endpoints really need to be public.
4. **Review DNS records and email security.** Keep only required records public, consider enabling DNSSEC, and tighten SPF from `~all` to `-all` once all legitimate senders are listed.
5. **Keep the WAF enabled and tuned.** ModSecurity already helps block naive attacks.
6. **Scan your own network regularly.** Periodic host discovery shows which devices are active.
7. **Investigate unknown devices.** Verify any unexpected device found during a scan.
8. **Document the network.** Keep topology and device information up to date.
9. **Test only with written authorization.**

---

# 💡 What I Learned

### 1. Footprinting
Each tool answers a different question: WHOIS shows who registered the domain, WhatWeb shows what it runs, Nslookup shows where it lives, Curl shows how it responds, Wafw00f shows what protects it, and DNSRecon shows how its DNS and email are set up.

### 2. Exposure is not the same as a vulnerability
A visible version number or DNS record is an observation. It only becomes a vulnerability once it is confirmed, so findings must be described carefully.

### 3. Host discovery
A ping scan (`-sn`) maps live hosts without scanning ports. It also showed that Nmap does not list the MAC address of the scanning machine itself.

### 4. Reading MAC addresses
The gateway had an unknown vendor because it uses a locally administered (randomised-type) MAC address.

### 5. Documentation
Good documentation covers what was done, what was found, what it means, what risk it creates and how to reduce it.

### 6. Authorization comes first
Scope is set in writing before any testing starts, and anything outside it is off limits.

---

# 📁 Repository Structure

```
.
├── README.md
└── screenshots/
    ├── 01-whois.png
    ├── 02-whatweb.png
    ├── 03-nslookup.png
    ├── 04-curl.png
    ├── 05-wafw00f.png
    ├── 06-dnsrecon.png
    ├── 07-ipconfig.png
    ├── 08-zenmap-scan.png
    ├── 09-topology.png
    └── 10-topology-legend.png
```

> Sensitive values in the screenshots (cookie value, local IP addresses, MAC address and Windows build number) have been redacted.

---

# 🔗 Tools & Resources

- **Kali Linux:** https://kali.org
- **Nmap / Zenmap:** https://nmap.org
- **WhatWeb:** https://github.com/urbanadventurer/WhatWeb
- **Wafw00f:** https://github.com/EnableSecurity/wafw00f
- **DNSRecon:** https://github.com/darkoperator/dnsrecon

---

# 👤 Author

**Mubassir**
Cybersecurity Intern, Networkwalks (Batch B084K)
Instructor: Waqas Karim CCIE

---

## 📌 Project Information

**Program Name:** Cybersecurity at Networkwalks | **Week:** 02 | **Project:** Footprinting & Network Scanning (W2-PM1, W2-PM5) | **Repository:** GitHub
