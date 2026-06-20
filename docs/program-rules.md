# Official Program Rules

> Source: Anthropic's HackerOne program page (verified June 2026)

---

## Program Rules (Exact)

### 1. Detailed Report Required
- Reproducible steps **mandatory**
- Without reproduction steps → not eligible for reward

### 2. Special Account Setup ⚠️ CRITICAL
```
When creating ANY test account on Anthropic's products:
  Email:  your-handle@wearehackerone.com
  Header: X-HackerOne-Handle: your-hackerone-username
```
Add this header to **every request** you send during testing.

```bash
# Example — every curl request must include:
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "X-HackerOne-Handle: your-h1-handle" \   # ← MANDATORY
  -H "content-type: application/json" \
  ...
```

### 3. One Vulnerability Per Report
- Submit separately, unless chaining is needed to show impact
- Chained bugs → one report showing full chain

### 4. Duplicates
- First report received wins
- If someone already reported it → you get $0
- **Check disclosed reports on HackerOne first!**

### 5. Same Root Cause = One Bounty
- 10 endpoints with same IDOR bug → one bounty, not 10
- Report the most impactful instance + mention others

### 6. Documentation Reports
- If your finding only updates documentation → **$100 flat reward**

### 7. Beta / Research Preview
- New features in Beta/Research Preview = **non-core for 1 month** from release
- Lower priority, may not be rewarded

### 8. Zero-Days
- Official patch exists for **< 7 days** → case-by-case basis
- Official patch exists for **< 30 days** (already known zero-day) → out of scope

### 9. ⚠️ AI-Written Reports = REJECTED
```
"False-positive and/or theoretical reports from automated scanners 
or written by AI will be closed as 'Not Applicable' at Anthropic's discretion."
```

**What this means for you:**
- AI agents se research = OK (private use)
- AI agents se report likhwana = NOT OK (will be rejected)
- Tujhe khud validate karna hai + khud likhna hai the report
- Working PoC required — no theoretical findings

### 10. PoC Mandatory
- Must validate all vulnerabilities yourself
- Working proof of concept required with every submission

---

## Important Notes (Current)

### Claude Code Permission Modal
```
⚠️ Anthropic is currently fixing permission modal bypass for command execution.
   Reports about this will likely be DUPLICATE → closed without bounty.
   Still submit if you find a novel variant, but expect duplicate closure.
```

### Auto-Accept-Edits Mode
```
Claude Code auto-accept-edits mode can modify all files in CWD.
This is NOT a vulnerability — it is expected behavior.
User who enables it is responsible. Do NOT report this.
```

### GitHub Actions
```
GitHub Action vulnerabilities in Anthropic's repos = non-core
UNLESS you can prove impact on core assets.
```

---

## Out of Scope — Complete List

These will be marked **Not Applicable** — don't submit:

| Category | Details |
|----------|---------|
| Best practices (no PoC) | SSL/TLS config, security headers without demonstrated impact |
| Physical attacks | Physical access required |
| Rate limiting | Only on non-authenticated endpoints |
| Insider compromise | Requires insider access |
| Social engineering | Phishing, vishing, etc. |
| Reflected file downloads | RFD attacks |
| Account takeover (brute force) | Brute force on accounts that aren't yours |
| DoS/DDoS | Any denial of service |
| Clickjacking | Only on pages with NO sensitive actions |
| Missing HttpOnly/Secure cookies | Without demonstrated impact |
| Dependency hijacking | Subdomain/package hijacking |
| Unpatched zero-days | No patch available OR patch < 30 days old |
| Jailbreaks | → Email modelbugbounty@anthropic.com instead |
| Hallucinations | Not a security vulnerability |
| Harmful content generation | → Email modelbugbounty@anthropic.com instead |

---

## Model Safety Issues (Jailbreaks)

**NOT on HackerOne** — separate process:

```
For jailbreaks, harmful content, safety concerns:
  Email: modelbugbounty@anthropic.com
  Include: Enough detail to replicate the issue
```

---

## Research Guidelines (What You Must Follow)

✅ **You MUST:**
- Test only to identify/discover vulnerabilities
- Use minimum exploitation needed to prove the bug exists
- Report any inadvertent data access
- Not retain any data you access

❌ **You MUST NOT:**
- Cause harm to Anthropic's systems
- Destroy, use, or exfiltrate data
- Disrupt services or user experience
- Run DoS attacks or high-traffic tools
- Attack accounts that aren't yours
- Social engineer any Anthropic employee
- Demand payment as condition of disclosure
- Be on OFAC sanctions list

---

## What Anthropic Promises You

- Acknowledge your report within **3 business days**
- Keep your name/contact private (unless you consent to credit)
- Not take legal action if you follow this policy (Safe Harbor)
- Keep you updated on investigation progress
- Credit you in public disclosure (with your permission)

---

## Disclosure Policy

- **Do NOT disclose publicly** until you get written confirmation from Anthropic
- Follow [HackerOne's disclosure guidelines](https://www.hackerone.com/disclosure-guidelines)
- Anthropic will never restrict you from reporting similar bugs at other companies

---

## Contact

| Purpose | Contact |
|---------|---------|
| Bug reports | HackerOne program page |
| Safety/jailbreak issues | modelbugbounty@anthropic.com |
| Policy questions | bugbounty@anthropic.com |
