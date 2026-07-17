# Portfolio Audit

A full review of every repository in the portfolio. The audit found problems in all of them.

## At a Glance

| Field | Detail |
| --- | --- |
| Scope | Every repo in the SOC portfolio |
| Standard | Claims traceable to evidence, not template compliance |
| Result | Formatting was fine. Claims outran evidence. |
| Fixed | 3 overclaimed titles, 6 MITRE tables, 5 broken SPL queries, scope notes throughout |

## What This Is

An audit is only useful if it can fail something.

The original checklist for this portfolio certified that every README followed the same template. Every repo passed. That result was worthless, because format consistency is not the thing that gets tested in an interview. The claims are.

This audit used a different standard: does every claim in a repo trace to evidence in that repo. Under that standard, nothing passed clean.

## What Was Found

**Three overclaimed titles.** Active Directory attack detection on a standalone host with no domain. Network segmentation with no VLANs and no zones. Authenticated vulnerability assessment on a default OpenVAS config against localhost. Each one is a claim an interviewer disproves in a single follow up.

**Six MITRE tables with mapping errors.** Three distinct error types: mapping my own tooling as adversary technique, claiming techniques nothing observed, and one that ran backwards. T1595 for running a scan. T1562.004 to describe a firewall existing, which is the technique for an attacker disabling it. T1566.001, spearphishing attachment, used four times for link based phishing.

**Unsupported claims.** "Production grade" across four repos, including one describing rules that fire on any match. Unsourced statistics. Readiness claims made inside the artifact being assessed.

**Five SPL queries that would not execute.** `case()` with wildcards, which does not glob. Missing `bucket` commands, so thresholds could never fire. `urldecode` on base64. A lookup table that does not exist in Splunk by default.

**Scope stated nowhere.** Lab environments presented as investigations. Constructed scenarios reading as incidents. A published training capture reading as a network I defend.

**Contradictions inside single repos.** A findings section claiming suspicious process activity where the process section never said anything was found. A report calling an incident contained three lines above ACTIVE INCIDENT IN PROGRESS. A remediation action that does not reach its own target.

[Full findings and per repo detail: portfolio_audit.md](portfolio_audit.md)

## The Finding

The portfolio was well documented and overclaimed.

Those are different failures, and a formatting checklist only catches the first one. Every repo had a clean README, a description, consistent file names, and a MITRE table. Several of those MITRE tables were wrong. The formatting was never the problem.

## What Improved

The strongest material in the portfolio is now the material that documents failure.

The firewall lab where a correct rule was applied, verified, and found to have done nothing, because UFW does not filter loopback. The rule was right and the rule was irrelevant, and only the rescan caught it.

The regex toolkit where a detection rule classified 2 of 4 known attacks and fired correctly the entire time. Visible only because coverage was tested against ground truth rather than against whether the rule produced output.

The Splunk alert build stating plainly that four rules firing on any match is correct for a lab and wrong for production.

The detection library stating that 4 of 14 queries have ever fired against the technique they detect.

None of that was in the original portfolio. All of it is more hireable than a rule that worked first time.

## Skill Assessment

| Area | Level | Evidence |
| --- | --- | --- |
| Splunk SPL | Strong | Field extraction, aggregation, risk scoring across three repos |
| Wireshark | Strong | TCP flags, RAT C2 in 15,512 packets, DHCP host identification |
| MITRE ATT&CK | Strong | Mapped across the portfolio, and now mapped correctly |
| Linux | Strong | auth.log, process state, file integrity, firewall, Bash tooling |
| Windows event analysis | Good | 4625, 4624, 4688, 4698, Sysmon. Chains rather than single events |
| Threat intelligence | Good | Multi platform IOC correlation to campaign attribution |
| Wazuh EDR | Good | Manager and agent deployed, rules fired, raw log read |
| Cloud | Developing | One CloudTrail investigation, one Sentinel lab. Zero Trust is design, not deployment |
| Detection engineering | Developing | Queries across 14 techniques. 4 validated against telemetry I generated |
| Python | Developing | Regex toolkit scripts. Not a strength yet |

Cloud and detection engineering were rated Strong before this audit. They are Developing now. An audit that upgrades its own author is not an audit.

## The Standard Going Forward

Every claim traces to evidence in the repo.

Every MITRE mapping is a technique observed, hunted for, or designed against, and the table says which.

Every lab says it is a lab.

Every negative finding is documented, because an investigation that only records hits is half an investigation.

Every limit is named by me rather than found by an interviewer.

## What This Portfolio Is

Lab work, documented honestly.

Real tools, real rules, real attacks generated and detected. Every environment is a lab and every repo now says so.

What it demonstrates: SPL fluency, packet level reading, chain correlation, ATT&CK mapping, and the judgment to say when a rule is untuned or a query has never been tested.

What it does not: production experience, tuned pipelines at scale, or an incident with a business on the other end.

The gap between those two lists is the job.

## Repository Structure

```
soc-29-portfolio-audit/
├── README.md
└── portfolio_audit.md
```

---

[![LinkedIn](https://img.shields.io/badge/LinkedIn-WilliamInCyber-blue?style=flat&logo=linkedin)](https://linkedin.com/in/WilliamInCyber)
[![X](https://img.shields.io/badge/X-@WilliamInCyber-black?style=flat&logo=x)](https://x.com/WilliamInCyber)
