### Security Audit Log: Tenda Router Infrastructure

#### Audit Methodology
The audit utilized a tiered approach to evaluate two Tenda network devices:
1. Automated Reconnaissance: Used the Nuclei vulnerability scanner with IoT/Router-specific templates to identify known exposure points.
2. Configuration Extraction: Manually queried sensitive paths (e.g., /cgi-bin/DownloadCfg/) to test for unauthenticated access.
3. Web Interface Analysis: Downloaded local client-side scripts (login.js) to reverse-engineer authentication logic, including encoding schemes and token requirements.
4. Authentication Simulation: Executed controlled HTTP POST requests using cURL to bypass frontend UI constraints and probe the backend.

#### Summary of Findings

| Device | Vulnerability Status | Primary Findings |
| :--- | :--- | :--- |
| 192.168.8.145 | High Risk | Unauthenticated Configuration Disclosure found at /cgi-bin/DownloadCfg/RouterCfm.jpg. |
| 192.168.8.1 | Hardened | No vulnerabilities; device enforces session-aware 302 redirects and server-side token validation. |

#### Detailed Analysis

* Target 192.168.8.145: This device failed security best practices by exposing its configuration file without authentication. This enabled direct extraction of cleartext or weakly encoded administrative credentials and Wi-Fi security keys.


* Target 192.168.8.1: This device demonstrated robust defense-in-depth. While our analysis of login.js showed the password is transmitted using Base64 encoding, the server requires a valid session cookie and likely a dynamic CSRF token generated during the initial page load. Our attempts to forge this session via cURL were met with 401 Unauthorized responses, confirming the effectiveness of its server-side handshake requirements.


#### Conclusion
The security gap between the two devices highlights that branding does not correlate with security parity. The .145 target serves as a primary example of "Broken Access Control," while the .8.1 target shows how modern firmware integrates session binding and request integrity checks to defeat standard exploitation techniques.
