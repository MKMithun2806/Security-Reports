#🎯 TARGET: dyse-two.vercel.app
**Tue Mar 24 03:36:51 +06 2026**

--- SURFACE ---

* **Hosts:** dyse-two.vercel.app
* **Live:** https://dyse-two.vercel.app
* **Ports:** 80, 443
* **Services:**
    * 80 → http → Vercel    * 443 → ssl/https → Vercel

--- FINDINGS ------ ATTACK PATHS ---

**Entry vector:** Information disclosure via Vercel deployment error**why:** Error responses leak internal deployment IDs and paths without authentication

**step 1:** Send a request to a non-existent endpoint to trigger 404 with Vercel error headers

**step 2:** Extract the `X-Vercel-Id` and `X-Vercel-Error` values from response headers

**outcome:** Exposure of internal deployment identifiers that could aid further reconnaissance or targeting

**confidence:** Medium (consistent behavior observed; requires only basic HTTP request)

--- INITIAL EXPLOIT COMMANDS ---**Goal:** Retrieve Vercel deployment error details```bash
curl -I https://dyse-two.vercel.app/nonexistent```
**note:** Look for `X-Vercel-Error: DEPLOYMENT_NOT_FOUND` and `X-Vercel-Id` headers indicating internal deployment data.

**Goal:** Test for open redirect to external domain
```bash
curl -s -D - "https://dyse-two.vercel.app/%2F%2Fevil.com" -o /dev/null
```
**note:** Check the `Location` header in the response; if it redirects to `https://evil.com`, an open redirect is present.

--- TOOLING SUGGESTIONS ---

* **Tool:** curl | **use:** Manual HTTP interaction and header analysis | **target:** dyse-two.vercel.app:443 | **templates:** N/A
* **Tool:** ffuf | **use:** Path and parameter discovery | **target:** dyse-two.vercel.app | **templates:** /usr/share/ffuf/wordlists/common.txt
* **Tool:** nuclei | **use:** Template-based vulnerability scanning | **target:** https://dyse-two.vercel.app | **templates:** http/exposures, http/misconfig

--- SEARCHSPLOIT QUICK HITS ---

```bash
searchsploit Vercel
searchsploit Next.js
```

--- NEXT MOVES ---

* **Enumerate:** Directory brute-forcing via `ffuf -u https://dyse-two.vercel.app/FUZZ -w /usr/share/wordlists/dirb/common.txt` (if credentials available) or use public wordlists without auth
* **Validate:** Check for exposed Vercel configuration files using:
```bash
curl -s https://dyse-two.vercel.app/vercel.json | jq .
```
* **Probe:** Test for subdomain takeover potential on related domains:
```bash
sublist3r -d vercel.app -o subs.txt && while read domain; do curl -s -o /dev/null -w "%{http_code}\\n" https://$domain; done < subs.txt
```

--- OPSEC ---

* **noise:** low (only passive header checks and minimal requests)
* **loud actions:** Aggressive directory brute-forcing with large wordlists or automated scanners triggering WAF/rate limits
* **stealthier alt:** Passive TLS certificate analysis via `censys` or `shodan` to gather intel without direct interaction

--- PWNABILITY SCORE ---| Metric | Score | Reason |
| :--- | :--- | :--- |
| **Exposure** | 18 | Host is publicly reachable with two standard web ports open; service fingerprint shows Vercel platform |
| **Service Risk** | 12 | Vercel is a managed platform; risk stems from potential misconfigurations rather than inherent flaws |
| **Vulnerabilities** | 5 | No known CVEs or exploitable findings identified in scan output |
| **Exploitability** | 8 | Limited to low-impact issues like information disclosure; no direct code execution paths evident |
| **Complexity** | 10 | Chaining info leaks to meaningful impact requires additional steps and assumptions |
**TOTAL:** 53/100
**RATING:** 🟡 Medium
**Reason:** The target presents a typical public-facing Vercel deployment with no critical vulnerabilities detected. Exposure is moderate due to open web services, but lack of exploitable flaws reduces immediate risk. Overall, the target offers low-to-medium pwnability, primarily useful for reconnaissance rather than direct compromise.

--- TL;DR ---
A Vercel-hosted site with open ports but no critical flaws, leaking only basic deployment identifiers via error responses.

--- OUTRO ---
Another day, another Vercel site shouting its deployment IDs into the void—thanks for the free intel, folks.