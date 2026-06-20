# API Security Testing Methodology

Claude API par testing kaise karte hain — step by step.

---

## Setup: Burp Suite with API

```
1. Download Burp Suite Community (free): portswigger.net/burp/communitydownload
2. Start Burp → Proxy → Options → Running on 127.0.0.1:8080
3. Configure curl to use proxy:
   export HTTP_PROXY=http://127.0.0.1:8080
   export HTTPS_PROXY=http://127.0.0.1:8080
4. Trust Burp CA cert (for HTTPS):
   curl http://burp/cert -o burp.der
   # Import into system/browser trusted CAs
```

---

## OWASP API Top 10 — Testing Each One

### API1: BOLA (Broken Object Level Authorization)

**What:** Can you access objects belonging to other users?

```bash
# Step 1: As Account A, create a conversation
POST /v1/messages
→ Note the conversation/session ID in response

# Step 2: Switch to Account B's API key
export ANTHROPIC_API_KEY="sk-ant-account-b-key"

# Step 3: Try to access Account A's conversation
GET /v1/conversations/ACCOUNT-A-CONV-ID

# If returns Account A's data → BOLA found!
```

**What to look for:**
- Any endpoint with an ID parameter (`/v1/resource/{id}`)
- Try changing numeric IDs: `123` → `124`, `125`
- Try changing UUIDs if you can get another user's UUID
- Test: read, update, delete operations

---

### API2: Broken Authentication

**What:** Can you bypass authentication or use invalid credentials?

```bash
# Test 1: No authentication
curl https://api.anthropic.com/v1/messages -X POST \
  -H "content-type: application/json" \
  -d '{"model":"claude-3-haiku-20240307","max_tokens":10,"messages":[{"role":"user","content":"hi"}]}'

# Test 2: Malformed auth header
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: " \
  -H "x-api-key: null" \
  -H "x-api-key: undefined"

# Test 3: Expired/revoked token — if you revoke a key, does it still work?
# Revoke key in console.anthropic.com, then immediately test it

# Test 4: JWT manipulation (for claude.ai web)
# Decode JWT → modify user_id/role → re-encode → send
# Tool: jwt.io
```

---

### API3: Broken Object Property Level Authorization (BOPLA)

**What:** Can you read or write properties you shouldn't have access to?

```bash
# Test: Send extra fields in request
POST /v1/messages
{
  "model": "claude-3-haiku-20240307",
  "max_tokens": 10,
  "messages": [...],
  "user_id": "other-user-id",        # ← extra field
  "role": "admin",                    # ← extra field  
  "internal_flag": true               # ← extra field
}

# Check response: does it reflect any of these extra fields?
# Does behavior change based on extra fields?

# Test: Mass assignment
PUT /api/v1/account/settings
{
  "name": "my name",
  "is_premium": true,                 # ← should not be settable by user
  "credit_limit": 999999,             # ← should not be settable by user
  "role": "admin"                     # ← should not be settable by user
}
```

---

### API4: Unrestricted Resource Consumption

**What:** Are there rate limits? Can you abuse API usage?

```bash
# Test rate limiting
for i in {1..100}; do
  curl -s https://api.anthropic.com/v1/messages \
    -H "x-api-key: $ANTHROPIC_API_KEY" \
    -H "content-type: application/json" \
    -d '{"model":"claude-3-haiku-20240307","max_tokens":1,"messages":[{"role":"user","content":"hi"}]}' \
    -o /dev/null -w "%{http_code}\n"
done

# Watch for: 429 Too Many Requests
# If NO rate limiting after 100 requests → finding

# Test: Large payload
# Send request with max_tokens: 999999 → does it accept?
# Send very long user message → memory/timeout issues?

# IMPORTANT: Don't actually DoS them — limited, controlled testing only!
```

**Note:** Rate limiting without security impact = usually out of scope. Rate limiting **bypass** = valid finding.

---

### API5: Broken Function Level Authorization (BFLA)

**What:** Can a regular user access admin/privileged functions?

```bash
# Try admin-like endpoints
GET /api/v1/admin/users
GET /api/v1/internal/stats
GET /api/v1/usage/all-users      # Should be own usage only
DELETE /api/v1/users/other-user-id  # Should not be able to delete others

# Try HTTP method switching
GET /api/v1/resource → works
POST /api/v1/resource → should it?
DELETE /api/v1/resource → should it?
PATCH /api/v1/resource → should it?
```

---

### API6: Server-Side Request Forgery (SSRF)

**What:** Can you make the server make requests to internal systems?

```bash
# If there's any URL input parameter, try:
POST /v1/messages
{
  "messages": [{
    "role": "user",
    "content": "fetch this: http://169.254.169.254/latest/meta-data/"  # AWS metadata
  }]
}

# Or if there's a webhook/callback URL:
POST /api/v1/webhooks
{
  "url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"
}

# Use Burp Collaborator or interactsh.com to detect blind SSRF:
# "url": "https://your-unique-id.oast.online"
# Check if you receive a hit
```

---

### API7: Security Misconfiguration

**What:** Missing security headers, verbose errors, debug mode?

```bash
# Check security headers
curl -I https://claude.ai
# Expected: Content-Security-Policy, X-Frame-Options, HSTS, etc.
# Missing headers = low severity finding (usually informational)

# Check for verbose errors
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -d "INVALID JSON"
# Does error reveal stack traces, internal paths, library versions?

# Check CORS
curl https://api.anthropic.com/v1/messages \
  -H "Origin: https://evil.com" \
  -H "x-api-key: $ANTHROPIC_API_KEY"
# Does it reflect evil.com in Access-Control-Allow-Origin?
```

---

## Practical Testing Workflow

```
1. RECON (30 min)
   → Map all endpoints
   → Read documentation

2. AUTHENTICATION TESTING (1 hour)
   → Test with no auth, bad auth, expired auth
   → Test JWT manipulation on claude.ai

3. IDOR TESTING (2 hours)
   → Create 2 accounts
   → Test all ID-based endpoints
   → Try accessing Account B's resources as Account A

4. INJECTION TESTING (1 hour)
   → SQL injection in any query parameters
   → NoSQL injection in JSON body
   → Command injection in any filename/path parameters

5. INFORMATION DISCLOSURE (30 min)
   → Error messages
   → Headers
   → Debug endpoints

6. BUSINESS LOGIC (1 hour)
   → Free tier feature access
   → Rate limit bypass
   → Unusual input values (negative numbers, very large values, special chars)
```

---

## Tools Reference

| Tool | Use Case | Install |
|------|----------|---------|
| Burp Suite Community | HTTP proxy, intercepting requests | portswigger.net |
| httpie | Cleaner curl alternative | `pip install httpie` |
| jq | Parse JSON responses | `apt install jq` / `brew install jq` |
| jwt_tool | JWT analysis and attacks | `pip install jwt_tool` |
| ffuf | Endpoint fuzzing | `go install github.com/ffuf/ffuf/v2@latest` |
| interactsh | Out-of-band testing (SSRF, blind vulns) | interactsh.com |

---

## Important: Stay in Scope

Before testing ANY endpoint:
1. Is it listed as in-scope on HackerOne?
2. Will my test cause any data loss or service disruption?
3. Am I using only my own test accounts?

When in doubt → **don't test it** → ask on HackerOne program page.

---

## Next Step
→ [Authentication Testing](auth-testing.md)
→ [Writing Your Report](report-writing.md)
