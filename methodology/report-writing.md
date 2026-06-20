# How to Write a Good HackerOne Report

A well-written report is the difference between:
- ❌ "N/A — cannot reproduce" (₹0 bounty)
- ✅ "Triaged — Critical — $5000 bounty"

---

## The Golden Rule

> **If you can't reproduce it from your own report, they can't either.**

Write your report so that a developer who has NEVER seen this bug can reproduce it in 5 minutes.

---

## Report Template

```markdown
## Summary
[1-2 sentences: What is the vulnerability and what is the impact?]

## Vulnerability Details
**Type:** [IDOR / XSS / SQLi / Auth Bypass / etc.]
**Severity:** [Critical / High / Medium / Low]
**CVSS Score:** [X.X - provide vector string]
**Affected Component:** [URL / API endpoint / feature]

## Impact
[What can an attacker do with this? Be specific.]
- Attacker can access [WHAT] belonging to [WHO]
- This affects [HOW MANY] users
- Data exposed includes: [list the data types]

## Steps to Reproduce
1. Log in as Account A (attacker)
2. Navigate to [URL]
3. Perform [ACTION]
4. Note the [ID/TOKEN/VALUE] in the response
5. Log in as Account B (victim) in a different browser/incognito
6. Replace [THEIR VALUE] with Account A's [ID/TOKEN/VALUE]
7. Observe that [IMPACT]

## Proof of Concept

### Request (Burp Suite / curl)
```
GET /api/v1/endpoint/VICTIM-ID HTTP/1.1
Host: api.anthropic.com
Authorization: Bearer ATTACKER-TOKEN
x-api-key: sk-ant-...
```

### Response
```json
{
  "user_id": "victim-user-id",
  "email": "victim@example.com",
  "private_data": "this should not be visible to attacker"
}
```

### Screenshot
[Attach screenshot showing the vulnerability]

## Remediation
[How should Anthropic fix this?]
- Implement proper authorization checks on [ENDPOINT]
- Verify that the requesting user has permission to access resource [ID]
- Use server-side session to validate ownership

## Additional Notes
[Anything else they should know]
- Tested on: [date]
- Affected regions: [all / specific]
- Related endpoints: [list similar endpoints that may have same issue]
```

---

## Section-by-Section Guide

### Summary (Most Important!)

❌ **Bad Summary:**
> "Found a bug in the API"

✅ **Good Summary:**
> "An authenticated attacker can access any user's private Claude conversation history by modifying the conversation_id parameter in the GET /api/v1/conversations/{id} endpoint, bypassing authorization checks. This exposes all messages, including potentially sensitive personal and business information."

**Formula:** `[Who] can [do what] by [how], bypassing [what protection], exposing [what data]`

---

### Steps to Reproduce

Rules:
1. **Number every step** — don't use bullet points
2. **Be specific** — "click the button" is bad; "click the 'Share' button in the top-right corner" is good
3. **Include exact values** — show the exact request/response
4. **Explain what to look for** — "Observe that the response contains victim's email address"
5. **Use 2 accounts** for IDOR — clearly label which account is "attacker" and which is "victim"

---

### Proof of Concept (PoC)

Always include:
- The **actual HTTP request** (from Burp Suite or curl)
- The **actual HTTP response** (especially the sensitive part)
- **Screenshots** if helpful
- A **video recording** for complex multi-step bugs

**PoC Code Example (Python):**
```python
import requests

ATTACKER_API_KEY = "sk-ant-attacker-key-here"
VICTIM_CONVERSATION_ID = "conv_abc123xyz"  # obtained from victim account

response = requests.get(
    f"https://api.anthropic.com/v1/conversations/{VICTIM_CONVERSATION_ID}",
    headers={
        "Authorization": f"Bearer {ATTACKER_API_KEY}",
        "x-api-key": ATTACKER_API_KEY,
    }
)

print(f"Status: {response.status_code}")
print(f"Victim's private data: {response.json()}")
```

---

### CVSS Score

Use the official CVSS calculator: [nvd.nist.gov/vuln-metrics/cvss](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator)

Always include the **vector string:**
```
CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:H/VA:N/SC:N/SI:N/SA:N
```

---

## Common Mistakes to Avoid

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| "This might be a bug" | Shows uncertainty, may be ignored | Test and confirm first |
| No PoC | Can't be reproduced → N/A | Always include curl/code |
| Using real victim's data | Illegal, unethical | Use your own 2nd test account |
| "I found a critical vuln" without severity score | Self-serving, unverified | Use CVSS calculator |
| Report without impact explanation | They don't know why it matters | Explain business impact |
| Submitting before fully testing | Wastes everyone's time | Test thoroughly first |
| Duplicate submission | Already reported | Search program's disclosed reports |

---

## Report Quality Checklist

Before submitting, verify:

- [ ] Title clearly describes the vulnerability type and impact
- [ ] Summary is 1-2 sentences and crystal clear
- [ ] Steps to reproduce are numbered and specific
- [ ] PoC includes actual HTTP request/response
- [ ] Screenshot or video attached
- [ ] CVSS score calculated with vector string
- [ ] Impact section explains WHO is affected and WHAT data is exposed
- [ ] Remediation suggestion included
- [ ] Used only test accounts (no real user data)
- [ ] Verified the endpoint is in scope

---

## After Submission

```
1. Monitor HackerOne notifications (email + app)
2. Respond quickly to triage team's questions
3. Provide additional PoC if requested
4. Don't disclose publicly until resolved
5. Be patient — triage takes 1-14 days usually
```

---

## Example: Real IDOR Report Format

```
Title: IDOR in GET /api/v1/conversations/{id} allows any authenticated 
       user to read private conversation history of other users

Summary:
An authenticated API user can read the private conversation history of any 
other user by guessing or obtaining their conversation_id. The endpoint 
/api/v1/conversations/{id} does not verify that the requesting user owns 
the conversation, only that they are authenticated.

Impact:
- Any authenticated user can read private conversations of all other users
- Conversations may contain sensitive business information, personal details, 
  medical discussions, legal information, etc.
- Affects all Claude.ai users (~estimated user count)

Steps to Reproduce:
1. Create Account A (attacker): attacker@test.com
2. Create Account B (victim): victim@test.com  
3. Log in as victim (Account B), start a conversation, note conversation_id
   from URL: https://claude.ai/conversation/conv_VICTIM123
4. Log out, log in as attacker (Account A)
5. Send request:
   GET /api/v1/conversations/conv_VICTIM123
   Authorization: Bearer ATTACKER_JWT_TOKEN
6. Observe: Full conversation history of victim is returned

CVSS: 7.5 (High) - AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:N/A:N

Remediation:
Implement server-side ownership check: verify that the authenticated user's 
user_id matches the conversation's owner_id before returning data.
```
