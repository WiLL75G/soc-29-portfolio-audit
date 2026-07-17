# SOC Analyst Portfolio Audit

Analyst: William | @WilliamInCyber | GitHub: WiLL75G
Date: July 2026

---

## Purpose

A full review of every repository in the portfolio against a quality standard.

The point of an audit is to find things. An audit where everything passes has either been run against a portfolio that is already perfect, or against a checklist written not to fail anything. The second is far more common.

This one found problems.

---

## The Checklist

```
README exists and is complete
Repo has a description
Folder structure is clean, no empty folders
File names are consistent
Screenshots named correctly and referenced
Profile README links resolve
MITRE ATT&CK mapping present and accurate
Claims are defensible against the evidence in the repo
Scope stated where the environment is a lab
```

The last two are the ones that matter. Everything above them is formatting.

The original checklist for this portfolio had a line reading "README follows locked in SOC Tier 1 Incident Report template." Every repo passed it. That line is why the first audit found nothing, because it measured whether the documents looked the same rather than whether they were true.

---

## Findings

### 1. Overclaimed titles, 3 repos

| Repo | Claimed | Actual |
|---|---|---|
| Password spray | Active Directory attack detection | Local accounts on a standalone host. No domain, no DC, `net use \\localhost\IPC$` |
| Firewall | Network segmentation | Host firewall on a single box. No VLANs, no subnets, no zones |
| Vulnerability scanning | Authenticated assessment | Default OpenVAS config against 127.0.0.1, no credentials configured |

All three retitled. Each was a claim an interviewer disproves in one follow up question, and the underlying work did not need the inflation.

### 2. MITRE mapping errors, 6 repos

Three distinct error types.

**Mapping my own tooling as adversary technique.**

T1595 active scanning, for running a vulnerability scan. The scanner was mine.

T1059.001 PowerShell, for using PowerShell to write a triage toolkit.

T1005 data from local system, for hashing a file during analysis.

The analyst is not the adversary. Mapping your own actions to attacker techniques inflates a coverage table with things nobody did.

**Claiming techniques nothing observed.**

T1190 exploit public facing application, where nothing was exploited.

T1078 valid accounts, in a lab where an account was created and never used to authenticate.

T1036 masquerading and T1070 indicator removal, on a malware triage where the sample was never executed.

**Backwards mapping.**

T1562.004 impair defences, used to describe UFW being present. That technique maps an attacker disabling the firewall, not the firewall existing.

**Wrong technique IDs.**

T1486 mapped to Collection and labelled VSS deletion. T1486 is Data Encrypted for Impact, under Impact. VSS deletion is T1490.

T1052 for insider exfiltration. T1052 is exfiltration over physical medium.

T1566.001 spearphishing attachment, used in four repos to describe link based phishing. That is T1566.002.

T1046 labelled Network Service Scanning. Renamed to Network Service Discovery in current ATT&CK.

T1548 abuse elevation control mechanism, for an IDOR finding. IDOR is broken access control, T1068.

All corrected. Mapping notes added distinguishing techniques **observed**, techniques **hunted for**, and techniques a control is **designed against**.

### 3. Unsupported claims

"Production grade" appeared across four repos. One of them describes four alert rules that trigger on any match with no threshold, which is the definition of untuned.

"Recruiter demonstrable," "portfolio ready," and "proves SOC readiness at the Tier 1 and Tier 2 level" appeared inside the artifacts being assessed. Never claim readiness in the thing being read.

Unsourced statistics: 3x recruiter visibility from applying within 24 hours. 100x cheaper to threat model before deployment. 97 percent faster detection than industry average.

All removed or qualified. The dwell time comparison in particular was against a 21 day industry figure dominated by months long undetected intrusions across enterprise estates, compared to a four analyst SOC watching a 500 user portal.

### 4. SPL that would not execute, 5 queries

Found by reading the SPL rather than running it. Which is its own finding.

`case()` with wildcard patterns, in two queries. `case()` does not glob. Every row fell through to the default tier. Corrected to `like()`.

Missing `bucket` commands, in two queries. Grouping by exact `_time` meant every event became its own row, so `count > 3` and a 100MB volume threshold could never fire.

`urldecode` applied to base64 output. `-EncodedCommand` takes base64, not URL encoding, so the eval returned the input unchanged. Splunk has no native base64 decode without an app. Corrected to extract the string and route to CyberChef or Event 4104.

A hunt query calling `| lookup dnsdomain_age` — a lookup table that does not exist in Splunk by default and has to be built from a domain registration feed. The query would fail on execution, not on data. Rewritten to hunt on domain rarity, which needs no lookup.

Three `case()` statements with unreachable bottom tiers, where the preceding `where` had already filtered above the threshold.

### 5. Scope not stated

Lab environments presented without saying so. Constructed scenarios reading as investigations. A published training PCAP from a malware traffic analysis exercise reading as a capture from a network I defend.

A DFIR investigation with a host, a user, a 47MB exfiltration, and a timeline to the minute, none of which happened on a machine I own.

Every repo now carries a scope note where the environment is a lab or the data is constructed.

### 6. Contradictions inside single repos

Linux triage: findings claimed "suspicious process activity observed." The process section only described what to look for. Nothing was found.

DFIR report: analyst notes said the campaign "has been contained" three lines above "Shift Status: ACTIVE INCIDENT IN PROGRESS."

SOC metrics: remediation action reads "tune top 4 rules to reduce FPs by 40 percent" against a target of dropping 91.5 percent to under 10. A 40 percent reduction reaches roughly 55 percent. The action does not reach its own target.

Vulnerability scan: DC traffic flagged as "possible lateral movement, warrants review" in observations, then "no evidence of lateral movement" in findings.

Splunk metrics: MTTD target of under 1 hour for High severity, result of 2.1 hours, marked as passing.

Compliance: GDPR Article 33 marked Met while ISO A.16 incident reporting marked Partial, both describing the same notification procedure.

All resolved by choosing the honest reading.

### 7. Aggregates hiding failures

The compliance mapping reported 83 percent overall across four frameworks. That number averages a 91 and a 63 across frameworks carrying entirely different penalties, and describes an organisation that does not exist. GDPR at 63 is what a regulator looks at.

The SOC metrics dashboard reported 96.9 percent SLA compliance, passing. High severity sat at 94.1 percent, failing, inside an aggregate carried over the line by low severity at 99.2.

Workload reported 40 alerts per analyst per day, passing against a 50 target. Peak day was 103 each. Averages hide peaks, and incidents happen on peak days.

### 8. Hard exclusions violated

Two repos built findings on Prefetch and Registry forensics, which are outside my defensible claim set. A DFIR timeline used Prefetch as execution evidence in three places and Registry run keys as the persistence stage. A detection library included a Registry run key query mapped to T1547.001.

Removed. The Windows Event ID evidence carries the same conclusions and is inside what I can defend.

PICERL appeared in two interview prep files. Replaced with the NIST SP 800-61 lifecycle, which the IR playbooks repo already uses.

---

## The Real Result

Not 28 of 28.

Every repo needed work. The formatting was fine throughout, and formatting was never the problem. The problems were claims that outran evidence, MITRE mappings that would not survive a follow up question, and scope that was implied rather than stated.

**The portfolio was well documented and overclaimed.** Those are different failures, and only one of them is visible from a checklist.

---

## What Was Fixed

Three titles corrected to what the labs actually are. Two repos renamed to match.

Six MITRE tables rebuilt with mapping notes distinguishing observed from hunted for from designed against.

Scope notes added wherever the environment is a lab or the data is constructed.

Every instance of "production grade" and every readiness claim removed.

Five SPL queries corrected. One rewritten to remove a dependency that does not exist in Splunk.

Internal contradictions resolved.

Prefetch and Registry evidence removed from two repos. PICERL removed from two career files.

Aggregates reported with their breakdowns, or removed where the aggregate described nothing.

---

## What Improved Because of It

The strongest material in the portfolio is now the material that documents failure.

**The firewall lab.** A UFW deny rule was applied to an exposed MySQL service. The rescan showed it still reachable. Root cause: UFW does not filter loopback by default. The rule was correct and the rule was irrelevant, because the traffic never passed through the chain the rule sits in. Only the rescan caught it, and the writeup keeps the failed attempt in.

**The regex toolkit.** A web attack detection rule classified 2 of 4 known attacks. It fired correctly every time it fired. The dashboard showed detections. Half the attacks went past silently, because the scanner User Agent regex covered sqlmap and Nikto and not curl or python-requests. It was only visible because coverage was measured against a known attack set rather than against whether the rule produced output.

**The Splunk alert build.** Four real time rules, all firing on any match. The writeup says plainly that this is correct for a lab and wrong for production, and that running them on live data first is what produces the baseline a real threshold comes from.

**The detection library.** 4 of 14 queries have ever fired against the technique they detect. The table says which.

None of that was in the original portfolio. All of it is more hireable than a rule that worked first time.

---

## Portfolio Coverage

| Domain | Repos |
|---|---|
| Detection engineering | SSH brute force, password spray, Splunk alerts, MITRE coverage, detection library |
| Incident response | Playbooks, shift simulation, DFIR timeline |
| Threat intelligence | OSINT investigation, threat modeling |
| Network security | Wireshark PCAP, port scan detection, firewall hardening |
| Malware | Static triage and host hunt, vulnerability scanning |
| Cloud | AWS CloudTrail, Zero Trust design |
| Endpoint | Wazuh EDR, PowerShell toolkit |
| Compliance | Framework mapping, SOC metrics |
| Tooling | SOC analyst toolkit, Linux triage toolkit, regex toolkit, IOC tracker |

---

## Skill Assessment

| Area | Level | Evidence |
|---|---|---|
| Splunk SPL | Strong | Field extraction with rex, aggregation, risk scoring. Three repos |
| Wireshark | Strong | TCP flag reading, RAT C2 in 15,512 packets, DHCP host identification |
| MITRE ATT&CK | Strong | Mapped across the portfolio, and now mapped correctly |
| Linux | Strong | auth.log, process state, /etc integrity, UFW, Bash tooling |
| Windows event analysis | Good | 4625, 4624, 4688, 4698, Sysmon. Chains rather than single events |
| Threat intelligence | Good | Multi platform IOC correlation to campaign attribution |
| Wazuh EDR | Good | Manager and agent deployed, rules fired, alerts read from raw log |
| Cloud | Developing | One CloudTrail investigation in a personal account. One Sentinel lab. Zero Trust is design, not deployment |
| Detection engineering | Developing | Queries written across 14 techniques. 4 validated against telemetry I generated |
| Python | Developing | Regex toolkit scripts. Not a strength yet |

Cloud and detection engineering were rated Strong before this audit. They are Developing now.

Cloud is one CloudTrail investigation in a personal account with 11 events. Detection engineering is a library that states 10 of 14 queries have never fired.

An audit that upgrades its own author is not an audit.

---

## The Standard That Replaced the Old One

The original checklist certified that every README followed the same template.

That was the wrong standard. Consistency of format is not consistency of honesty, and a portfolio can be perfectly formatted and entirely overclaimed. This one was.

The standard now:

Every claim traces to evidence in the repo.

Every MITRE mapping is a technique observed, hunted for, or designed against, and the table says which.

Every lab says it is a lab.

Every negative finding is documented, because an investigation that only records hits is half an investigation.

Every limit is named by me rather than found by an interviewer.

---

## What This Portfolio Is

Lab work, documented honestly.

Real tools, real rules, real attacks generated and detected. Every environment is a lab, and every repo now says so.

What it demonstrates: SPL fluency, packet level reading, chain correlation, ATT&CK mapping, and the judgment to say when a rule is untuned or a query has never been tested.

What it does not demonstrate: production experience, tuned pipelines at scale, or an incident with a business on the other end of it.

The gap between those two lists is the job.
