# Severity Rating Guide

HackerOne aur Anthropic ke liye sahi severity kaise decide karein.

---

## Quick Reference

| Severity | CVSS Range | Example |
|----------|-----------|---------|
| Critical | 9.0–10.0 | RCE without auth, Mass PII leak |
| High | 7.0–8.9 | IDOR exposing all user data, Auth bypass |
| Medium | 4.0–6.9 | IDOR single user, Stored XSS low impact |
| Low | 0.1–3.9 | Self-XSS, Missing header with no impact |
| Informational | N/A | Best practice suggestion, minor config |

---

## CVSS v4.0 Calculator

Official tool: https://www.first.org/cvss/calculator/4.0

### Key Metrics Explained Simply

**Attack Vector (AV):**
- `Network (N)` = Internet se directly attack (highest score)
- `Adjacent (A)` = Same network chahiye
- `Local (L)` = Local access chahiye
- `Physical (P)` = Physical device access chahiye

**Attack Complexity (AC):**
- `Low (L)` = Easy, koi special conditions nahi
- `High (H)` = Difficult, specific conditions chahiye

**Privileges Required (PR):**
- `None (N)` = Login nahi chahiye (highest score)
- `Low (L)` = Regular user login chahiye
- `High (H)` = Admin access chahiye

**User Interaction (UI):**
- `None (N)` = Victim ko kuch nahi karna (highest score)
- `Passive (P)` = Victim normal kaam kare toh trigger
- `Active (A)` = Victim ko kuch click/do karna padega

**Impact (Confidentiality / Integrity / Availability):**
- `High (H)` = Complete data access/loss
- `Low (L)` = Partial impact
- `None (N)` = No impact

---

## Common Scenarios with Scores

### Scenario 1: IDOR with Predictable IDs
```
Endpoint: GET /api/v1/conversations/12345
Issue: Change 12345 → 12346 = access other user's conversation

CVSS Vector: AV:N/AC:L/PR:L/UI:N/VC:H/VI:N/VA:N
Score: ~7.1 → HIGH

Reasoning:
- AV:N = Internet accessible
- AC:L = Easy, just change number
- PR:L = Need to be logged in
- UI:N = No victim interaction needed
- VC:H = Full conversation content exposed
```

### Scenario 2: IDOR with Unpredictable IDs (UUID)
```
Endpoint: GET /api/v1/conversations/a1b2c3d4-e5f6-7890-abcd-ef1234567890
Issue: UUID is hard to guess, but if obtained → still unauthorized access

CVSS Vector: AV:N/AC:H/PR:L/UI:N/VC:H/VI:N/VA:N
Score: ~5.9 → MEDIUM

HackerOne may adjust: if UUID is leaked somewhere else → AC:L → HIGH
```

### Scenario 3: Authentication Bypass
```
Issue: Special crafted request bypasses login entirely

CVSS Vector: AV:N/AC:L/PR:N/UI:N/VC:H/VI:H/VA:N
Score: ~9.1 → CRITICAL

Reasoning:
- PR:N = No credentials needed
- VC:H + VI:H = Can read AND modify data
```

### Scenario 4: Stored XSS
```
Issue: Malicious script stored in conversation, executes for all who view it

CVSS Vector: AV:N/AC:L/PR:L/UI:P/VC:L/VI:L/VA:N
Score: ~4.6 → MEDIUM

Notes:
- UI:P because victim must view the conversation (passive interaction)
- If affects many users → raise to HIGH based on impact
```

### Scenario 5: Sensitive Data in Error Messages
```
Issue: API error response includes internal server path and database schema

CVSS Vector: AV:N/AC:L/PR:L/UI:N/VC:L/VI:N/VA:N
Score: ~3.1 → LOW

Notes: Usually informational unless data is very sensitive
```

### Scenario 6: Mass PII Exposure
```
Issue: Endpoint returns ALL users' email addresses without pagination/auth check

CVSS Vector: AV:N/AC:L/PR:N/UI:N/VC:H/VI:N/VA:N
Score: ~8.6 → HIGH

HackerOne override: May rate as CRITICAL if millions of users affected
(HackerOne allows exceeding CVSS for massive PII impact)
```

---

## HackerOne Special Rules

### When CVSS Can Be Overridden (Higher)
- Mass PII exposure affecting millions of users
- Systemic vulnerability affecting entire user base
- Vulnerability in safety-critical AI feature

### When CVSS Can Be Downgraded (Lower)
- Requires specific, hard-to-meet conditions
- Limited real-world exploitability
- Already mitigated by other controls

### Systemic Issues Rule
```
Same vuln in 10 endpoints:
→ Report as 1 finding (most impactful endpoint)
→ Mention other affected endpoints
→ Scored at single finding level
→ NOT 10x the bounty for 10 reports
```

---

## Severity by Vulnerability Type

| Vulnerability | Typical Severity | Notes |
|--------------|-----------------|-------|
| RCE (unauthenticated) | Critical | Rare in API |
| Auth bypass | Critical | Depends on access gained |
| IDOR (all users data) | High | Scale matters |
| IDOR (single user) | Medium-High | Depends on data type |
| SQLi | High-Critical | Depends on data access |
| Stored XSS | Medium | Depends on impact |
| Reflected XSS | Low-Medium | Usually needs interaction |
| CSRF (sensitive action) | Medium | Rare with SameSite cookies |
| SSRF (internal) | High | If internal systems accessible |
| SSRF (blind) | Medium | Limited info gained |
| Open redirect | Low | Usually informational |
| Missing security headers | Informational | No direct impact |
| JWT alg:none | Critical | If server accepts it |
| Secrets in code | High | If valid and sensitive |
| Expired cert | Low | Usually informational |
| Rate limit missing | Low-Medium | Depends on what's unprotected |

---

## Before Submitting: Severity Self-Check

Ask yourself:
1. What's the WORST thing an attacker can do with this bug?
2. Does it require victim to do something? (if yes → lower severity)
3. Does it require special conditions? (if yes → lower severity)
4. How many users are affected? (more = higher)
5. What type of data is exposed? (PII/financial = higher)
6. Is it in scope? (if no → don't submit)

---

## CVSS String Examples for Copy-Paste

```
Critical RCE:     CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:H/SI:H/SA:H
High IDOR:        CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N  
Medium IDOR UUID: CVSS:4.0/AV:N/AC:H/AT:N/PR:L/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N
Medium XSS:       CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:P/VC:L/VI:L/VA:N/SC:N/SI:N/SA:N
Low Info Disc:    CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:L/VI:N/VA:N/SC:N/SI:N/SA:N
```

Use FIRST.org calculator to verify: https://www.first.org/cvss/calculator/4.0
