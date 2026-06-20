# Anthropic Bug Bounty Program — Complete Guide

> Official Program: [hackerone.com/anthropic](https://hackerone.com/anthropic?type=team)
> Program went **PUBLIC** on HackerOne in May 2026 — previously invite-only with NDA.

---

## Two Separate Tracks

Anthropic ka bug bounty **do alag programs** mein divided hai. Bahut important hai yeh samajhna:

```
Track 1: PRODUCT SECURITY  →  HackerOne pe (traditional security bugs)
Track 2: MODEL SAFETY       →  Separate application required (jailbreaks)
```

**Dono alag hain — alag submission process, alag rewards!**

---

## Track 1: Product Security (HackerOne)

### In-Scope Assets

| Asset | URL / Description |
|-------|-------------------|
| **Claude.ai** | Web application — chat interface |
| **Anthropic API** | api.anthropic.com — REST API |
| **Claude Code** | CLI tool for developers |
| **Desktop Apps** | Official Claude desktop clients |
| **Mobile Apps** | iOS and Android Claude apps |
| **Internal Infrastructure** | Anthropic's servers and systems |
| **Official SDKs** | Anthropic-developed SDKs |
| **MCP Integrations** | Anthropic-developed MCP servers only |
| **Chrome Extensions** | Official Anthropic extensions |

### Out-of-Scope

❌ **NEVER test these:**
- Third-party MCP servers (not made by Anthropic)
- Archived/deprecated open source repositories
- Social engineering attacks
- Low-severity informational findings (missing headers, etc.)
- Model behavior / content issues → belongs to Track 2
- Jailbreaks → belongs to Track 2
- Hallucinations → not a security bug
- Harmful content generation → Track 2, not here

### Reward Structure

Maximum reward: **$15,000** (Product Security track)

Rewards based on **CVSS severity**:

| CVSS Severity | CVSS Score | Estimated Reward |
|--------------|-----------|-----------------|
| Critical | 9.0 – 10.0 | Up to $15,000 |
| High | 7.0 – 8.9 | $2,500 – $10,000 |
| Medium | 4.0 – 6.9 | $500 – $2,500 |
| Low | 0.1 – 3.9 | $100 – $500 |
| Informational | N/A | $0 |

> ⚠️ Final reward is at Anthropic's discretion. CVSS is a guideline, not a guarantee.

### Claude Code — Special Focus Area

Claude Code ke liye **specific critical issues** dhundhe jaate hain:

1. **Unauthorized command execution** — Claude Code bina permission ke system commands run kare
2. **Invisible tool usage** — User ko pata nahi chalta Claude kya tools use kar raha hai
3. **Permission bypasses** — Security boundaries tod ke restricted operations karna
4. **Sandbox escapes** — Claude Code ke isolated environment se bahar nikalna

> Claude Code findings = HIGH priority, likely HIGH/CRITICAL severity

---

## Track 2: Model Safety Bug Bounty (Separate Program)

### What This Is

Yeh **traditional security testing nahi hai**. Yeh hai:
- Novel, universal jailbreak attacks dhundhna
- Claude ke Constitutional Classifiers bypass karna
- Harmful information extract karna specific domains mein

### High-Risk Domains (What They Care About)

```
CBRN = Chemical, Biological, Radiological, Nuclear
+ Cybersecurity attack capabilities
```

### Reward Structure

**Up to $35,000 per novel, universal jailbreak!**

Sliding scale based on:
- Kitna detailed aur accurate harmful information extract hua
- Kitna universal hai (multiple prompts pe kaam kare)
- Kitna novel hai (naya technique)

### How to Apply (NOT HackerOne)

```
Step 1: Apply via Google Form (rolling applications)
Step 2: Sign NDA (Non-Disclosure Agreement)
Step 3: Get free model access for authorized red-teaming
Step 4: Test using provided confidential question set
Step 5: Submit findings
```

### Important Rules for Track 2

- Cannot disclose jailbreaks publicly
- Cannot share testing questions
- Cannot reveal other participants
- Must not disclose classifier details
- All under strict NDA

> **Beginner ke liye:** Track 1 (HackerOne) se start karo. Track 2 ke liye NDA aur dedicated access chahiye.

---

## What to Look For in Product Security (Track 1)

### Priority 1: IDOR / Authorization Issues

```bash
# Test: Can Account A access Account B's conversations?
GET /api/v1/conversations/ACCOUNT_B_CONV_ID
Authorization: Bearer ACCOUNT_A_TOKEN

# If yes → HIGH severity IDOR
```

### Priority 2: Claude Code Security

```bash
# Test: Does Claude Code execute commands without showing user?
# Test: Can you bypass permission prompts?
# Test: Can a malicious repo escape Claude Code sandbox?
```

### Priority 3: Authentication Bypass

```bash
# Test: JWT manipulation on claude.ai
# Test: API key validation edge cases
# Test: Session management issues
```

### Priority 4: API Security (OWASP Top 10)

```bash
# BOLA, BOPLA, SSRF, BFLA
# Rate limit bypass
# Mass assignment
# Broken function level authorization
```

### Priority 5: Information Disclosure

```bash
# Stack traces in errors
# Internal paths/IPs in headers
# Sensitive data in API responses
# Secrets in client-side code
```

---

## Setup Guide

### Step 1: Create Accounts

```
1. HackerOne account → hackerone.com (free)
2. Join Anthropic program → hackerone.com/anthropic
3. 2x Claude.ai test accounts (for IDOR testing)
4. API key → console.anthropic.com (free tier)
```

### Step 2: Tools

```bash
# Burp Suite Community (free) — HTTP proxy
# Download: portswigger.net/burp/communitydownload

# Set API key
export ANTHROPIC_API_KEY="sk-ant-your-key-here"

# Test API access
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{"model":"claude-3-haiku-20240307","max_tokens":10,"messages":[{"role":"user","content":"hi"}]}'
```

### Step 3: Read Before Testing

- ✅ [HackerOne Platform Standards](hackerone-standards.md) — IDOR rules, CVSS, systemic issues
- ✅ [Severity Rating Guide](severity-rating.md) — CVSS v4.0 scoring
- ✅ [Report Writing](../methodology/report-writing.md) — How to submit

---

## Submission Process (Track 1)

```
1. Find vulnerability → verify manually
2. Create PoC (proof of concept)
3. Calculate CVSS score
4. Go to hackerone.com/anthropic → "Submit Report"
5. Fill template: Title, Severity, Description, Steps, PoC
6. Submit → wait for triage (1-14 days)
7. Respond to follow-up questions quickly
8. Bounty paid after fix is verified
```

---

## Key Stats

- **Program went public:** May 2026
- **Max reward (Product Security):** $15,000
- **Max reward (Model Safety):** $35,000
- **Medium + High = ~87%** of all paid rewards
- **Critical = ~1%** (rare but highest paid)

---

## Resources

- [HackerOne Program Page](https://hackerone.com/anthropic?type=team)
- [Model Safety Bug Bounty Details](https://support.claude.com/en/articles/12119250-model-safety-bug-bounty-program)
- [Anthropic Responsible Disclosure Policy](https://www.anthropic.com/responsible-disclosure-policy)
- [HackerOne Platform Standards](hackerone-standards.md)
- [How to Write a Report](../methodology/report-writing.md)
