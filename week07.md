# Week 07 – Wireless Networks

| | |
|---|---|
| **Student** | Zeel Pareshbhai Jani (12331098) |
| **Group partner** | Drashti Patel (12329332) |
| **Tutor** | Dr David Ling |

## Task 1 – Knowledge Test

Completed the Week 7 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – View Wi-Fi Details

I scanned nearby access points from a Windows laptop using PowerShell:

```powershell
netsh wlan show networks mode=bssid    # SSID, BSSID, band, channel and radio type of all visible APs
netsh wlan show interfaces             # data rate of the AP I am connected to
```

### AP details recorded

| Field | AP 1 (home network) | AP 2 (phone hotspot) | AP 3 (neighbour) |
|---|---|---|---|
| SSID | StrongVirus_5G | Nothing Phone (2a) | Airtel_Karshan |
| BSSID | fa:46:5c:23:cd:3a | 62:b3:bb:a8:e9:f1 | 30:bd:13:f1:c8:e7 |
| Band | 5 GHz | 5 GHz | 5 GHz |
| Channel | 157 | 48 | 36 |
| Radio type | 802.11ax (Wi-Fi 6) | 802.11ax (Wi-Fi 6) | 802.11ax (Wi-Fi 6) |
| Signal | 40% | 99% | 78% |
| Data rate | 866.7 Mbps | 866.7 / 400 Mbps | 54 Mbps (max listed) |
| Security | WPA2-Personal | WPA3-Personal | WPA2-Personal |

**Observation:** `StrongVirus_5G` was broadcasting the same SSID on both a 2.4 GHz BSSID (channel 6) and a 5 GHz BSSID (channel 157). This is an example of band steering – client devices can automatically select the better-performing band.

## Task 3 – Use Wi-Fi Access Point

I reviewed the settings on a wireless access point/router to identify the options that matter when designing a Wi-Fi network, informed by the scan in Task 2 (38 networks visible, most of them on the crowded 2.4 GHz band).

### Screenshot of AP settings

![AP wireless settings](week07/week7-task3-ap-settings.png)

### Important settings and what I would change

| Setting | Current | Change to | Reason |
|---|---|---|---|
| Security mode | WPA2-Personal | WPA3-Personal | WPA3 fixes WPA2's offline dictionary-attack weakness; the hotspot AP captured in Task 2 already uses it, showing it is available on current hardware. |
| Channel selection (2.4 GHz) | Auto | Manual, fixed to 1, 6 or 11 | The scan showed 11+ nearby networks crowded onto channels 1, 6 and 11. Auto-selection can still land on a congested channel, so manually picking the least-used one reduces interference. |
| Band steering / dual SSID | Same SSID on both 2.4 GHz and 5 GHz (seen on StrongVirus_5G) | Keep enabled | Lets devices pick the faster 5 GHz band when in range and fall back to 2.4 GHz for longer range – a good default for a mixed-device home network. |
| Guest network isolation | Not assessed on the captured APs | Enable, on a separate SSID/VLAN | Keeps guest and IoT devices off the main LAN, limiting exposure if a guest device is compromised. |

## Task 4 – Self-Evaluation of Teamwork

### Generative AI prompt and output

Prompt used: *"Generate a list of ways to improve teamwork in a university group project."*

![Generative AI prompt and output](week07/week7-task4-genai-prompt.png)

AI-suggested improvements (from the output above):

1. Set clear roles – assign responsibilities based on each member's strengths.
2. Set common goals – agree on what the group needs to achieve and by when.
3. Communicate regularly – use a group chat tool (WhatsApp, Teams, Discord).
4. Create a shared schedule – track tasks, deadlines and progress in one place.
5. Divide the workload fairly.
6. Hold regular short meetings to discuss progress, problems and next steps.
7. Respect different ideas and encourage everyone to share opinions.
8. Support struggling members rather than leaving them behind.
9. Resolve conflicts early, calmly and constructively.
10. Review each other's work and give constructive feedback before submission.
11. Keep everyone accountable – make responsibilities and deadlines visible.
12. Celebrate progress and milestones to maintain motivation.

### Comparison against our team's practice

Our team has two members: myself and my partner, Drashti Patel.

**What we already do:** we split earlier tasks by strength – one of us led the network diagrams while the other focused on the ping/packet-capture analysis – and then reviewed each other's sections before submission. This lines up with the AI's *"set clear roles"* and *"review each other's work"* points.

**What we do not do yet:** we do not have a shared task board or a fixed check-in schedule. Coordination has mostly happened through direct messages as tasks come up, rather than through a tracked board with visible deadlines, so it is easy to lose sight of what is still outstanding (for example the optional ARP table task slipping to a later week).

### GitHub contribution review

> **TODO:** insert a screenshot of the repository's **Insights → Contributors** page showing commits from both team members, e.g. `week07/week7-task4-contributors.png`, and reference it here.

### Evaluation and action items

With only two of us, workload is easier to keep even than in the larger teams in the class, but the commit history should be checked to confirm contributions are actually balanced rather than assumed. A two-person team also has less redundancy – if one person is unavailable, a task simply does not progress, which is the main risk we need to manage.

Action items:

1. Start using GitHub Issues or a shared checklist so outstanding work is tracked rather than remembered informally.
2. Agree a fixed short check-in (e.g. at the start of each tutorial) instead of ad-hoc messaging, addressing the AI's *"create a shared schedule"* and *"hold regular meetings"* suggestions.

## Task 5 – Continue Your Project

I showed my current project progress to my tutor, Dr David Ling, during the tutorial to get feedback.

> **TODO:** write the tutor's feedback here.
