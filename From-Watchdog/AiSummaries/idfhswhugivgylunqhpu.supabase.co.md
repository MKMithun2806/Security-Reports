#🎯 TARGET: idfhswhugivgylunqhpu.supabase.co**Tue Mar 24 03:20:16 +06 2026**

--- SURFACE ---

* **Hosts:** idfhswhugivgylunqhpu.supabase.co
* **Live:** https://idfhswhugivgylunqhpu.supabase.co
* **Ports:** 80, 443, 8080, 8443
* **Services:**
    * 80 → http → cloudflare
    * 443 → ssl/https → cloudflare
    * 8080 → http-proxy → cloudflare
    * 8443 → ssl/https-alt → cloudflare

--- FINDINGS ---

--- ATTACK PATHS ---

**Entry vector:** None identified**why:** No vulnerabilities were discovered in the scan results.

**step 1:** N/A

**step 2:** N/A

**outcome:** N/A

**confidence:** Low (no exploitable findings indicate high confidence in absence of trivial attack paths)

--- INITIAL EXPLOIT COMMANDS ---

**Goal:** No exploit commands available due to lack of identified vulnerabilities
```bash
echo "No vulnerabilities found to exploit"
```
**note:** The scan did not reveal any exploitable vulnerabilities in the exposed services.

--- TOOLING SUGGESTIONS ---* **Tool:** nuclei | **use:** vulnerability scanning | **target:** idfhswhugivgylunqhpu.supabase.co | **templates:** http, misconfig
* **Tool:** nmap | **use:** deeper port and service scan | **target:** idfhswhugivgylunqhpu.supabase.co | **templates:** 
* **Tool:** whatweb | **use:** technology fingerprinting | **target:** idfhswhugivgylunqhpu.supabase.co | **templates:** 

--- SEARCHSPLOIT QUICK HITS ---

```bash
searchsploit cloudflare
searchsploit supabase
```

--- NEXT MOVES ---

* **Enumerate:** Check for misconfigured Supabase buckets or APIs via `curl -s https://idfhswhugivgylunqhpu.supabase.co/rest/v1/` (if credentials available) or use passive reconnaissance via `crt.sh` for subdomains
* **Validate:** Test for open Supabase admin interfaces using:
```bash
curl -k -s https://idfhswhugivgylunqhpu.supabase.co/functions/v1/ | grep -i "supabase"
```
* **Probe:** Analyze SSL/TLS configuration for weaknesses:
```bash
nmap --script ssl-enum-ciphers -p 443,8443 idfhswhugivgylunqhpu.supabase.co
```

--- OPSEC ---

* **noise:** medium (active nmap scan triggered Cloudflare responses, indicating detectable traffic)
* **loud actions:** active exploitation attempts, brute forcing, or aggressive scanning
* **stealthier alt:** passive reconnaissance using SSL certificate transparency logs and public API enumeration

--- PWNABILITY SCORE ---

| Metric | Score | Reason |
| :--- | :--- | :--- |
| **Exposure** | 10 | Internet-facing with multiple ports, but obscured by Cloudflare |
| **Service Risk** | 10 | Services hidden behind Cloudflare; actual risk unknown |
| **Vulnerabilities** | 0 | No vulnerabilities discovered in scan |
| **Exploitability** | 0 | No exploitable vulnerabilities identified |
| **Complexity** | 0 | High complexity due to Cloudflare WAF and lack of target service visibility |

**TOTAL:** 20/100
**RATING:** 🟢 Low
**Reason:** The target presents a low pwnability score due to the absence of identifiable vulnerabilities and the obscuring effect of Cloudflare, which prevents direct service enumeration and exploitation. While the infrastructure is internet-exposed, the lack of actionable findings significantly reduces immediate risk. Further assessment would require bypassing or obtaining details about the origin services behind the proxy.

--- TL;DR ---
No exploitable vulnerabilities found; target obscured by Cloudflare proxy.

--- OUTRO ---
All that cloudflare and still no secrets to steal? Disappointing.