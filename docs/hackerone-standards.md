# HackerOne Platform Standards

> Source: [HackerOne Detailed Platform Standards](https://docs.hackerone.com/en/articles/8369826-detailed-platform-standards)

These are the **official rules** HackerOne uses to evaluate bug reports. Understanding these will help you:
- Rate your findings correctly
- Avoid having reports marked as invalid
- Get maximum bounty for your work

---

## 1. IDOR (Insecure Direct Object Reference) Standards

### What is IDOR?
IDOR happens when an app uses predictable or guessable identifiers to access objects, and doesn't check if the user has permission.

**Example:**
```
GET /api/v1/users/12345/profile
→ Change 12345 to 12346
→ See another user's private profile
→ IDOR!
```

### HackerOne IDOR Rules

**Rule 1: Unpredictable IDs = Valid Vulnerability**
- If the ID is a UUID, random hash, or otherwise unpredictable → still a valid IDOR
- The unpredictability doesn't mean it's "safe"

**Rule 2: Attack Complexity Defaults to HIGH for Unpredictable IDs**
```
Unpredictable ID (UUID, hash) → Attack Complexity: HIGH → Lower CVSS score
Predictable ID (1, 2, 3...) → Attack Complexity: LOW → Higher CVSS score
```

**Rule 3: Exceptions That Lower Complexity**
Even with unpredictable IDs, Attack Complexity can be rated LOW if:
- IDs are leaked elsewhere in the application
- IDs are visible in logs, URLs, or API responses
- IDs can be enumerated through another endpoint
- IDs are exposed in error messages

### Practical Tips for IDOR Testing

```
Step 1: Create 2 test accounts (Account A and Account B)
Step 2: As Account A, perform an action (create post, update profile, etc.)
Step 3: Note the ID/URL of the created object
Step 4: Log into Account B
Step 5: Try to access/modify Account A's object using its ID
Step 6: If successful → IDOR!
```

---

## 2. Systemic Issues

### What Are Systemic Issues?
When the **same vulnerability class** exists in **multiple similar areas** of the application.

**Example:** Same IDOR bug exists in:
- `/api/v1/posts/{id}` 
- `/api/v1/comments/{id}`
- `/api/v1/messages/{id}`
- `/api/v1/files/{id}`

### HackerOne Payment Rules for Systemic Issues

```
First 3 reports of same vuln class → Full bounty each
Reports 4+ (same class, same root cause) → Discretionary bonus only
```

### Best Practice

**Don't submit 10 separate reports for the same vulnerability class.**

Instead:
1. Submit **1 detailed report** for the most impactful instance
2. In the report, mention the other affected endpoints
3. This shows thoroughness AND follows HackerOne standards

---

## 3. Bug Chains

### What Are Bug Chains?
Multiple vulnerabilities combined to achieve a bigger impact.

**Example:**
```
Bug 1: SSRF (low severity by itself)
    +
Bug 2: Internal metadata API accessible via SSRF
    +
Bug 3: AWS credentials in metadata API
    =
Critical: Complete cloud account takeover!
```

### HackerOne Bug Chain Rules

- **Evaluate by overall impact**, not individual bug severity
- If Bug 1 enables Bug 2, the chain earns Bug 2's severity + bonus
- Earlier reports in a chain MAY get **re-evaluated** and additional payment once the full chain is understood

### Reporting Bug Chains

```markdown
## Summary
This report chains 3 vulnerabilities to achieve [FINAL IMPACT].

## Chain Overview
1. [Bug 1] → enables → [Bug 2]
2. [Bug 2] → enables → [Bug 3]
3. Combined impact: [CRITICAL/HIGH]

## Individual Vulnerabilities
### Vulnerability 1: ...
### Vulnerability 2: ...
### Vulnerability 3: ...

## Combined Proof of Concept
[Step by step exploit]
```

---

## 4. Client Application Network Vulnerabilities

Issues that bypass encryption protections (without requiring certificate pinning to be disabled) are valid vulnerabilities.

**Examples:**
- MitM due to improper TLS validation
- SSL stripping in mobile app
- Cleartext transmission of sensitive data
- Certificate pinning bypass (if the pinning wasn't intentionally disabled)

---

## 5. Responsible Disclosure for Third-Party Components

### Rule
If you find a vulnerability in a **third-party library/component** that Anthropic uses:

```
Scenario A: No public patch, no public exploit
  → Report to the component's VENDOR first
  → Then report to Anthropic (as a notification)

Scenario B: Public patch already exists / Public exploit exists
  → Report directly to Anthropic
  → They should have already patched it
```

### Payment
Companies should compensate for third-party component reports because **they chose to integrate that dependency**.

---

## 6. Sensitive Data Exposure Standards

### Critical Severity Trigger
Vulnerabilities exposing the following to **unprivileged users** across a **substantial user base**:

| Data Type | Severity |
|-----------|----------|
| Social Security Numbers (SSN) | Critical |
| Credit card numbers | Critical |
| Physical home addresses | Critical |
| Medical records | Critical |
| Passwords (plaintext/reversible) | Critical/High |
| Email addresses (large scale) | High/Medium |
| General PII | Medium/High |

> **Note:** Critical severity for PII can **exceed standard CVSS scores**. HackerOne allows programs to pay above CVSS when real-world impact is massive.

---

## 7. CVSS Scoring Basics

CVSS (Common Vulnerability Scoring System) is the standard scoring method.

### CVSS v4.0 Key Metrics

```
Attack Vector (AV):     Network(N) > Adjacent(A) > Local(L) > Physical(P)
Attack Complexity (AC): Low(L) > High(H)
Attack Requirements:    None(N) > Present(P)
Privileges Required:    None(N) > Low(L) > High(H)
User Interaction:       None(N) > Passive(P) > Active(A)
```

### Score Ranges

| Score | Severity |
|-------|----------|
| 9.0 - 10.0 | Critical |
| 7.0 - 8.9 | High |
| 4.0 - 6.9 | Medium |
| 0.1 - 3.9 | Low |
| 0.0 | None/Informational |

### Quick CVSS Examples

```
Remote Code Execution (unauthenticated): 
  AV:N / AC:L / PR:N / UI:N → Critical (9.8)

IDOR with unpredictable ID:
  AV:N / AC:H / PR:L / UI:N → Medium (5.x)

IDOR with predictable ID:
  AV:N / AC:L / PR:L / UI:N → High (7.x)

Local privilege escalation:
  AV:L / AC:L / PR:L / UI:N → High (7.x)
```

---

## 8. Common Report Outcomes

| Outcome | Meaning |
|---------|---------|
| **Triaged** | Company accepted and is reviewing |
| **Resolved** | Bug fixed, bounty incoming |
| **Duplicate** | Already reported by someone else |
| **Informational** | Real but too low impact for bounty |
| **Not Applicable** | Not a security issue in this context |
| **Spam** | Poor quality, not a real issue |

---

## Next Steps

→ [Anthropic's Specific Program](anthropic-program.md)
→ [How to Write a Good Report](../methodology/report-writing.md)
→ [Severity Rating Guide](severity-rating.md)
