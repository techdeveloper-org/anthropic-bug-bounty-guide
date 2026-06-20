# What Is a Bug Bounty Program?

## Simple Explanation (Bilkul Basic Se)

Sochlo ek company hai — jaise Anthropic. Unke paas ek product hai (Claude AI). Ab is product mein koi security flaw ho sakta hai jo unhone notice nahi kiya.

**Bug Bounty = Company tujhe REWARD deti hai agar tu unki website/app mein security problem dhundh kar unhe bataye.**

Yeh legal hai, authorized hai, aur helpful hai.

---

## Analogy

> Bank ek building banata hai. Unhe security expert hire karta hai building test karne ke liye. Expert ek secret door dhundta hai, bank ko batata hai, aur reward milta hai.

Bug bounty exactly aise hi hai — but **anyone** participate kar sakta hai.

---

## How HackerOne Works

[HackerOne](https://hackerone.com) ek **platform** hai jahan companies apna bug bounty program host karti hain.

```
You (Security Researcher)
    ↓
Find a vulnerability in Anthropic's product
    ↓
Submit report on HackerOne
    ↓
Anthropic reviews it (usually 3-14 days)
    ↓
If valid → You get BOUNTY (money) + Hall of Fame credit
    ↓
Anthropic fixes the bug → Everyone is safer
```

---

## What Is "Scope"?

Every bug bounty program has a **scope** — what you're allowed to test and what you're NOT.

### In Scope (You CAN test these):
- Listed API endpoints
- Listed web properties
- Listed mobile apps
- Specific product features

### Out of Scope (You CANNOT test these):
- Third-party services
- Social engineering attacks
- Physical security
- DoS/DDoS attacks
- Anything not explicitly listed

> ⚠️ Always read the program's scope page on HackerOne BEFORE testing.

---

## What Are Rewards?

Rewards vary by:
- **Severity** of the bug (Critical > High > Medium > Low)
- **Impact** (how many users affected, what data exposed)
- **Quality** of the report (clear PoC, good explanation)

Typical ranges (varies by program):
| Severity | Typical Range |
|----------|--------------|
| Critical | $5,000 - $50,000+ |
| High | $1,000 - $10,000 |
| Medium | $200 - $2,000 |
| Low | $50 - $500 |
| Informational | $0 (but still helpful!) |

---

## Basic Terminology

| Term | Meaning |
|------|---------|
| **Vulnerability / Bug** | Security flaw in the software |
| **PoC** | Proof of Concept — showing the bug is real and exploitable |
| **Scope** | What's allowed to test |
| **Out of Scope** | What's NOT allowed to test |
| **Triage** | Company reviewing your report |
| **Bounty** | The money reward |
| **N/A** | Not Applicable — report was invalid/duplicate |
| **Duplicate** | Someone already reported the same bug |
| **Informational** | Real issue but not severe enough for bounty |
| **CVSS** | Common Vulnerability Scoring System — severity rating (0-10) |
| **Responsible Disclosure** | Reporting to company FIRST, then public after fix |

---

## Legal Protection

When you participate in a company's bug bounty program on HackerOne:

✅ You have **written authorization** to test
✅ The company has agreed NOT to take legal action against you (if you follow rules)
✅ HackerOne provides the **Safe Harbor policy**

This is why **never test without authorization** — testing without a bug bounty program = illegal hacking.

---

## Next Steps

Now that you understand what a bug bounty is:

1. → [How HackerOne Platform Standards Work](hackerone-standards.md)
2. → [Anthropic's Specific Program Details](anthropic-program.md)
3. → [How to Find Vulnerabilities](../methodology/recon.md)
