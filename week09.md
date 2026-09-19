# Week 09 – Attacks and Vulnerabilities

| | |
|---|---|
| **Student** | Zeel Pareshbhai Jani (12331098) |
| **Group partner** | Drashti Patel (12329332) |
| **Tutor** | Dr David Ling |

**Project scenario:** retail store network (POS systems, in-store security cameras, wireless network, customer database).

## Task 1 – Knowledge Test

Completed the Week 9 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – CIA Protections

| Asset | Protection | Reason |
|---|---|---|
| Asset 1: POS terminals and POS server | Availability | If the POS system is down, the store cannot process sales, causing lost revenue and queues at checkout. |
| Asset 2: Customer payment card data | Confidentiality | Card numbers must not be exposed to attackers or unauthorised staff; leakage leads to fraud and PCI-DSS penalties. |
| Asset 3: Customer personal and loyalty details | Confidentiality | A customer's name, contact details and purchase history should not be visible to other customers or unrelated staff. |
| Asset 4: Security camera system | Availability | If the cameras are down, no recording exists if a theft or incident occurs, weakening evidence for investigation. |
| Asset 5: Security camera footage (stored recordings) | Confidentiality | Footage may capture staff, customers and till activity; unauthorised viewing breaches customer and staff privacy. |
| Asset 6: Sales and inventory database | Integrity | Stock counts and prices must be accurate; tampering could let an attacker sell items at incorrect prices or hide theft. |
| Asset 7: In-store Wi-Fi/router (staff and guest network) | Availability and Integrity | Availability keeps POS and card payments online; integrity stops an attacker changing DNS/routing settings to intercept traffic. |
| Asset 8: Employee login credentials (POS/back office) | Confidentiality | Credentials must not be exposed, as they are the main way an attacker gains access to the systems above. |

## Task 3 – Threat Sources and Motivation

| Threat source | Motivation |
|---|---|
| 1. Cybercriminal / organised crime group | Financial gain – steal customer payment card data to sell or use for fraud. |
| 2. Dishonest or disgruntled employee | Steal cash or stock, leak customer data, or sabotage systems out of revenge after a grievance. |
| 3. Opportunistic shoplifter | Disable or blind the security cameras/network to steal merchandise undetected. |
| 4. Script kiddie / opportunistic hacker | Little financial motive – scans for known unpatched vulnerabilities (e.g. an outdated router) for a challenge or to build a botnet. |
| 5. Competitor business | Gain an unfair advantage by stealing sales figures, supplier pricing or customer lists. |
| 6. Nearby member of the public | Wants free/unrestricted use of the store's guest Wi-Fi, or is curious about what else is on the network. |

## Task 4 – Explore Vulnerabilities

### CVE 1 – Critical: D-Link DIR-878 stack-based buffer overflow

| Field | Details |
|---|---|
| CVE ID | CVE-2026-90692 |
| CVE description | A stack-based buffer overflow in the `SetDynamicDNSIPv6Settings` function of the D-Link DIR-878 (firmware 120B05). Oversized `IPv6Address`/`Hostname` values overflow a fixed-size stack buffer, and the flaw is remotely exploitable. |
| Date | 14 September 2026 |
| CVSS score | 9.4 (Critical) – CVSS v4.0: `AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:H/VA:H` |
| CIA impact | Confidentiality: High · Integrity: High · Availability: High |
| CWE | CWE-121 – Stack-based Buffer Overflow |
| Company | D-Link |
| Product affected | DIR-878, a consumer/small-business Wi-Fi router – the kind of device a small retail store could use for its in-store network and internet connection. |
| Simple explanation | The router's settings page accepts a hostname/IP address for Dynamic DNS without checking its length before copying it into a fixed-size memory area. A long, crafted value overwrites nearby memory, letting an attacker crash the router or potentially run their own code on it. |
| Detection / mitigation | D-Link has ended support for the DIR-878, so no patch is expected. Disable remote/WAN management, restrict access to the admin interface to trusted internal IPs only, and replace the device with a currently supported router. |

### CVE 2 – High: Hikvision wireless AP / networking products command execution

| Field | Details |
|---|---|
| CVE ID | CVE-2026-16843 |
| CVE description | Some Hikvision wireless access point and networking products allow authenticated command execution because crafted packets containing OS commands are not properly validated. |
| Date | 31 July 2026 |
| CVSS score | 7.2 (High) – CVSS v3.1: `AV:N/AC:L/PR:H/UI:N/C:H/I:H/A:H` |
| CIA impact | Confidentiality: High · Integrity: High · Availability: High |
| CWE | CWE-78 – Improper Neutralization of Special Elements used in an OS Command (OS Command Injection) |
| Company | Hikvision |
| Product affected | Hikvision wireless access points/networking products – used to extend Wi-Fi coverage, which a store could use to connect POS terminals or cameras wirelessly. |
| Simple explanation | Once logged in with valid credentials, an attacker can send a specially built network packet that the device mistakenly runs as a system command instead of treating it as data, giving the attacker control of the device. |
| Detection / mitigation | Apply Hikvision's firmware update from the vendor advisory, enforce strong unique admin credentials so an attacker is less likely to obtain valid logins, and keep the AP's management interface off the public internet. |

### CVE 3 – Medium: Hikvision DS-2CD camera privilege escalation

| Field | Details |
|---|---|
| CVE ID | CVE-2026-57599 |
| CVE description | A privilege escalation vulnerability in some Hikvision DS-2CD series cameras. Incorrect permission allocation lets an attacker who has authenticated via SSH escalate to full control of the device. |
| Date | 22 July 2026 |
| CVSS score | 6.6 (Medium) – CVSS v3.1: `AV:N/AC:H/PR:H/UI:N/C:H/I:H/A:H` |
| CIA impact | Confidentiality: High · Integrity: High · Availability: High |
| CWE | CWE-269 – Improper Privilege Management |
| Company | Hikvision |
| Product affected | DS-2CD series security cameras – the store's own CCTV cameras. |
| Simple explanation | The camera's operating system does not correctly restrict what an SSH-authenticated user is allowed to do, so a lower-privileged account can escalate itself to full admin rights and take over the camera, including its recordings. |
| Detection / mitigation | Update to Hikvision's patched firmware per the vendor advisory, disable SSH on cameras unless actively needed, and use unique, non-default SSH credentials on every device. |

## Task 5 – Vulnerability Disclosures

### Why might a vendor take time before making a vulnerability public?

Vendors usually delay public disclosure so they have time to build, test and roll out a fix before attackers know exactly what to exploit. Publishing details of an unpatched flaw immediately would hand a ready-made attack blueprint to criminals while customers are still exposed. Time is also needed to coordinate a release across firmware versions, notify downstream partners, and prepare support and communications to handle customer questions once the advisory goes out.

### What is a reasonable time?

A reasonable window is generally around **90 days** from the point a researcher privately reports the issue. This is the default period used by Google Project Zero and reflected in most coordinated disclosure frameworks. It is long enough for most vendors to patch routine bugs, but short enough to stop indefinite stalling. More complex flaws (e.g. deep firmware or hardware issues) may reasonably need longer if the vendor is actively engaging and can show real progress, whereas a vendor that goes silent or refuses to act does not deserve the same extension.

### Should the researcher make it public without the vendor's permission?

If a vendor ignores the report or drags on well past a reasonable period with no patch or communication, I think the researcher is justified in disclosing publicly, even without the vendor's permission. Silent or indefinite delay leaves users unknowingly at risk, and public pressure is often the only thing that motivates a slow vendor to act. Even so, responsible disclosure still means giving the vendor a fair, clearly communicated deadline first and, where possible, withholding the finer exploitation details (proof-of-concept code, exact payloads) even in the public write-up, so the disclosure warns and pressures the vendor without directly arming attackers.

### Bug bounty programs

Bug bounty programs help by giving researchers a clear, legitimate channel and a financial incentive to report privately in the first place, which reduces the number of vulnerabilities that end up sold or leaked instead.

### Conclusion

Coordinated vulnerability disclosure – private report, agreed deadline, then public advisory – gives the best balance between protecting users and keeping vendors accountable.

**References:** [OWASP Vulnerability Disclosure Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Vulnerability_Disclosure_Cheat_Sheet.html) · [Microsoft Coordinated Vulnerability Disclosure](https://www.microsoft.com/en-us/msrc/cvd)
