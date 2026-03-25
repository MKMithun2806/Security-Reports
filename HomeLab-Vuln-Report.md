 🔐 Internal Security Assessment Report

**Target:** `192.168.1.15`

**Classification:** `CONFIDENTIAL – Internal Security Review`

**Assessor:** `Red Team Operator (Mithun)`

**Date:** `2026-03-03`

---

## 🗒️ Executive Summary

An internal security assessment was conducted against host `192.168.1.15`, a homelab environment hosting containerized services on a **Raspberry Pi (aarch64)** architecture.

The engagement successfully moved from reconnaissance to **full container compromise**. The discovery of a misconfigured `code-server` instance provided an unauthorized interactive shell, allowing for configuration theft and potential source code manipulation.

## **Key Risk Indicators**

|**Severity**|**Finding**|
|---|---|
|🔴 **CRITICAL**|**Unauthorized code-server access (Container Shell)**|
|🔴 **CRITICAL**|**Next.js Authorization Bypass (CVE-2024-XXXX / ≤15.2.2)**|
|🟠 **HIGH**|Missing Security Headers (Clickjacking/MIME Risk)|
|🟡 **MEDIUM**|Information Disclosure (robots.txt & Framework Headers)|

---

## 🛠️ Scope & Methodology

## **Scope**

- **Target:** `192.168.1.15` (Private LAN)
    
- **Services:** Next.js (80), Gitea (3005), code-server (Docker)
    
- **Methodology:** PTES (Penetration Testing Execution Standard)
    

## **Tooling**

- `Nmap 7.98` | `Nikto v2.5.0` | `SearchSploit` | `Gobuster` | `Curl`
    

---

## 🌐 Network Architecture

Code snippet

```
graph TD
    A[Attacker: MKS] -->|Internal LAN| B(192.168.1.15: Raspberry Pi)
    B --> C{Docker Engine}
    C --> D[Port 80: Next.js App]
    C --> E[Port 3005: Gitea Instance]
    C --> F[Exposed: code-server]
    F -->|Exploited| G[Interactive Shell: abc@3d37c73de677]
```

---

## 🔍 Port & Service Enumeration

Full TCP port scanning revealed the following active entry points:

|**Port**|**Service**|**Version**|**Notes**|
|---|---|---|---|
|**80**|HTTP|Next.js|Framework: 15.2.2 (Vulnerable)|
|**3005**|HTTP|Gitea|Version: 1.25.4|

> **Service Verification:**
> 
> `curl http://192.168.1.15:3005/api/v1/version`
> 
> `Response: {"version":"1.25.4"}`

---

## 🚩 Detailed Findings

## **F-01: Unauthorized code-server Access** 🔴 `CRITICAL`

- **Component:** `code-server` (Dockerized)
    
- **Vulnerability:** Authentication Bypass / Null Authentication
    
- **Impact:** Interactive shell access.
    

The red team discovered that the IDE environment was accessible without a password challenge. This allowed for direct terminal spawning on the container.

**Container Intel:**

- **Kernel:** `Linux 6.8.0-1048-raspi aarch64`
    
- **User:** `abc`
    
- **Persistence Path:** `/config` (contains `.gitconfig`, `workspace`, and `EvilPortal-HTML-Files`)
    

---

## **F-02: Next.js Auth Bypass (Exploit 52124)** 🔴 `CRITICAL`

- **Component:** Next.js ≤ 15.2.2
    
- **Vulnerability:** Middleware Authorization Bypass
    
- **Impact:** Access to `/admin` and protected API routes.
    

---

## ⛓️ Attack Chain Reconstruction

1. **Recon:** Nmap identified `80` and `3005`.
    
2. **Fuzzing:** Directory brute-forcing uncovered the hidden `code-server` path.
    
3. **Exploitation:** Accessed the IDE; launched an integrated terminal.
    
4. **Post-Ex:** Enumerated the `/config` directory, leaking internal Git credentials and "EvilPortal" configurations.
    

---

## 🛡️ Remediation Recommendations

## **Immediate Actions**

- **Authentication:** Enforce `auth: password` in `code-server` config and use a strong secret.
    
- **Patching:** Upgrade Next.js to the latest stable version (>15.2.2).
    
- **Credential Rotation:** Rotate all Git and environment passwords found in `/config`.
    

## **Technical Hardening**

**Apply Security Headers (Next.js config):**

JavaScript

```
headers: [
  { key: "X-Frame-Options", value: "DENY" },
  { key: "X-Content-Type-Options", value: "nosniff" },
  { key: "Content-Security-Policy", value: "default-src 'self';" }
]
```

---

## 📈 Risk Matrix

|**Impact \ Likelihood**|**Low**|**Medium**|**High**|
|---|---|---|---|
|**High**||F-02 (Next.js)|**F-01 (Shell Access)**|
|**Medium**|F-04 (MIME)|F-03 (Headers)||
|**Low**|F-07 (Methods)|||
