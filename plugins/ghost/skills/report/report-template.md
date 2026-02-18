# Security Report

## Scan Information
- **Repository**: <repo_name>
- **Commit**: <short_sha>
- **Date**: <date>
- **Scans Included**: <list of scan types that ran, e.g., deps, secrets, code>

---

## Executive Summary

<1-2 paragraphs summarizing:
- Overall security posture of the repository
- Count of high-confidence findings by severity (high, medium)
- Which scan types contributed findings
- Business context from repo.md if available (criticality level, sensitive data types at risk)
- Key areas of concern and recommended immediate actions>

---

## Critical & High Findings

<For each high-severity, high-confidence finding, inline the full substantive content grouped by finding type as shown below. Findings from different scan types sit together under this heading, sorted by type (deps → secrets → code).>

### SCA findings — include:

#### <Title>

- **Severity**: High
- **Package**: <package@version>
- **Lockfile**: <lockfile path>
- **CVEs**: <CVE identifiers>

**Vulnerability Summary**

<2-4 sentences describing the vulnerability>

**Exploitability Analysis**

<Usage context, vulnerable functions called, attack vector (entry point, data flow, impact)>

| Factor | Assessment | Evidence |
|--------|-----------|----------|
| <factor> | <assessment> | <evidence> |

**Remediation**

- Upgrade command: `<command>`
- Target version: <version>
- Test areas: <areas to test after upgrade>

**Vulnerable Code**

```
<vulnerable code snippet>
```

---

### Secret findings — include:

#### <Title>

- **Severity**: High
- **Location**: <file:line>
- **Secret Type**: <type>

**Description**

<2-4 sentences describing the finding>

**Secret Details**

- Redacted Value: <redacted>
- Type: <type>
- Entropy: <entropy score>

**Code Context**

```
<code context snippet>
```

**Risk Assessment**

| Factor | Assessment |
|--------|-----------|
| Real Secret | <yes/no + evidence> |
| Hardcoded | <yes/no + evidence> |
| Production Code Path | <yes/no + evidence> |
| Exposure Evidence | <details> |

**Remediation**

1. Rotate the credential immediately
2. Remove from source code
3. Scrub from git history
4. Audit access logs

---

### Code findings — include:

#### <Title>

- **Severity**: High
- **CWE**: <CWE identifier>
- **Location**: <file:line:function>

**Description**

<2-4 sentences describing the vulnerability>

**Vulnerable Code**

```
<vulnerable code snippet>
```

**Remediation**

<Remediation guidance>

**Fixed Code**

```
<fixed code snippet>
```

**Verification**: <verdict> — <reason>

---

<If no high-severity findings exist, write:>

No critical or high severity findings were identified across all scans.

---

## Medium Findings

<For each medium-severity finding, write a full subsection using the same per-type structure as Critical & High above. Each finding gets its own subsection — do NOT use a condensed table. Include description, location, code context, and remediation for every finding.>

<If no medium-severity findings exist, write:>

No medium severity findings were identified across all scans.

---

## Scan Coverage

| Scan Type | Status | Candidates Scanned | Confirmed Findings | False Positives Filtered |
|-----------|--------|--------------------|--------------------|--------------------------|
| Dependencies (SCA) | <ran / not run> | <count> | <count> | <count> |
| Secrets | <ran / not run> | <count> | <count> | <count> |
| Code (SAST) | <ran / not run> | <count> | <count> | <count> |

### Methodology

<For each scan type that ran, include a 1-2 sentence methodology note drawn from the per-scan reports. For example:>

- **Dependencies (SCA)**: <brief methodology note>
- **Secrets**: <brief methodology note>
- **Code (SAST)**: <brief methodology note>

---

## Bug Bounty Submission Briefs

<For each high or medium severity finding that is verified or confirmed-exploitable, generate a structured submission brief suitable for bug bounty platforms. Each brief should be self-contained and ready to paste into a HackerOne, Bugcrowd, or Intigriti report form.>

<If no findings qualify, omit this section entirely.>

### <Finding Title>

**Severity**: <Critical / High / Medium>
**CWE**: <CWE identifier>
**CVSS Vector** (estimate): <CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:N or similar>
**CVSS Score** (estimate): <numeric score, e.g. 8.1>

**Summary**

<1-2 sentence description of the vulnerability suitable for a report title and summary field.>

**Steps to Reproduce**

<Numbered steps an external tester can follow to reproduce the issue. Include:
1. Authentication requirements (role, account type)
2. Specific endpoint, URL, or code path
3. Request details (method, headers, body) or actions to take
4. Expected vs actual behavior
5. How to observe the vulnerability was triggered>

**Impact**

<2-3 sentences describing the real-world security impact: what data is at risk, what actions an attacker can take, and what the blast radius is. Tie impact to business context from repo.md when available.>

**Proof of Concept**

<Code snippet, curl command, or request/response pair demonstrating the vulnerability. If live validation was performed, include the captured evidence. If not, include the vulnerable code path and a sample payload.>

```
<PoC code, curl command, or request/response>
```

**Remediation**

<Concise fix recommendation.>

---

*Report generated by Ghost Security*
