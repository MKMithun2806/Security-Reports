# 🎯 TARGET: money.x.com

Sun Mar 22 23:55:36 +06 2026

--- SURFACE ---

* **Hosts:** api.money.x.com, p.money.x.com, sdn.money.x.com, webhooks.money.x.com
* **Live:** https://api.money.x.com, https://webhooks.money.x.com, https://sdn.money.x.com, https://p.money.x.com
* **Ports:** 80, 443, 8080, 8443
* **Services:**
    * 80 → http → cloudflare
    * 80 → http → unknown
    * 443 → ssl/https → cloudflare
    * 443 → ssl/http → Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
    * 443 → ssl/https → unknown
    * 8080 → http-proxy → cloudflare
    * 8443 → ssl/https-alt → cloudflare--- FINDINGS ---

--- ATTACK PATHS ---

**Entry vector:** HTTP to HTTPS redirect misconfiguration on p.money.x.com:80

**why:** Server redirects all HTTP traffic to invalid URL https:/// (double slash), potentially causing errors or bypassing security controls if mishandled.

**step 1:** Send request to http://p.money.x.com to observe 301 redirect to https:///

**step 2:** Test if redirect destination interprets attacker-controlled input (e.g., via path/query parameters) as valid internal resource

**outcome:** Potential open redirect, SSRF, or information leakage via error messages

**confidence:** Medium (misconfiguration observed; exploit impact requires validation)

--- INITIAL EXPLOIT COMMANDS ---

**Goal:** Confirm redirect behavior
```bash
curl -v http://p.money.x.com```
**note:** Observe 301 response and Location header pointing to https:///

**Goal:** Validate redirect consistency across paths/parameters
```bash
curl -v "http://p.money.x.com/test?param=value"
```
**note:** Verify Location header remains https:/// regardless of input

--- TOOLING SUGGESTIONS ---

* **Tool:** curl | **use:** Test HTTP redirects and responses | **target:** p.money.x.com:80 | **templates:** 
* **Tool:** nmap | **use:** Scan for open ports/services on subdomains | **target:** money.x.com | **templates:** vuln, http, ssl
* **Tool:** ffuf | **use:** Fuzz for hidden endpoints/subdomains | **target:** *.money.x.com | **templates:** 

--- SEARCHSPLOIT QUICK HITS ---

```bashsearchsploit golang
searchsploit cloudflare
```

--- NEXT MOVES ---

* **Enumerate:** Discover more subdomains via passive sources (crt.sh, dnsdumpster) or brute force with wordlists via: `ffuf -u https://FUZZ.money.x.com -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 1234` (if credentials available) or use public wordlists and search engines
* **Validate:** Check SSL/TLS versions and ciphers on p.money.x.com:443 using:
```bash
nmap --script ssl-enum-ciphers -p 443 p.money.x.com
```
* **Probe:** Check for HTTP methods allowed on p.money.x.com:80:
```bash
curl -v -X OPTIONS http://p.money.x.com
```

--- OPSEC ---

* **noise:** low (single HTTP requests resemble normal traffic)
* **loud actions:** port scanning all subdomains with nmap
* **stealthier alt:** passive DNS enumeration via certificate transparency logs and search engines

--- PWNABILITY SCORE ---

| Metric | Score | Reason |
| :--- | :--- | :--- |
| **Exposure** | 20 | Multiple public-facing hosts with live web services |
| **Service Risk** | 10 | Cloudflare lowers risk for most services; Golang service on p.money.x.com:443 is custom/unknown |
| **Vulnerabilities** | 0 | No CVEs or known vulns identified in initial scan |
| **Exploitability** | 5 | Misconfiguration observed but clear exploitation path undefined |
| **Complexity** | 10 | Triggering misconfiguration is simple; developing reliable exploit requires more context |

**TOTAL:** 45/100
**RATING:** 🟡 Medium
**Reason:** Target exhibits significant exposure with multiple internet-accessible services, but no critical vulnerabilities were found in initial reconnaissance. The Golang service on p.money.x.com:443 presents unknown risk, and the HTTP redirect misconfiguration requires further validation to determine exploitability for chaining attacks.

--- TL;DR ---
money.x.com exposes multiple services with misconfigurations but no critical vulnerabilities identified yet.

--- OUTRO ---
Even their redirects seem lost in the cloud.