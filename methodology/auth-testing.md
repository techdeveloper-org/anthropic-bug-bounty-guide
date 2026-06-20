# Authentication & Authorization Testing

Auth bugs = high/critical severity. Ye section claude.ai ke authentication system ko test karne ka guide hai.

---

## Authentication vs Authorization

```
Authentication = "TU KAUN HAI?" → Login, API keys, sessions
Authorization  = "TU KYA KAR SAKTA HAI?" → Permissions, RBAC
```

Both important hain. Auth bypass = usually Critical.

---

## API Key Testing

### What to Test

```bash
# 1. Key format validation
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: sk-ant-" \          # Too short
  -H "x-api-key: AAAA..." \           # Wrong prefix
  -H "x-api-key: " \                  # Empty
  -X POST

# 2. Key in wrong header
curl https://api.anthropic.com/v1/messages \
  -H "Authorization: Bearer sk-ant-YOUR-KEY" \ # Wrong header name
  -X POST

# 3. Key in URL parameter (should NOT work, but check)
curl "https://api.anthropic.com/v1/messages?api_key=sk-ant-YOUR-KEY" -X POST

# 4. After revoking key — does it still work?
# Step 1: Note your key
# Step 2: Revoke it in console.anthropic.com
# Step 3: Immediately test it (within seconds)
# If it still works briefly → race condition in key revocation
```

### Key Leakage Testing

```bash
# Check if API key appears in any response headers or body
curl -v https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  ... 2>&1 | grep -i "sk-ant\|api.key\|api_key"

# Check error messages
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: INVALID" \
  -X POST
# Does error message reveal anything about key format/validation?
```

---

## Session Testing (claude.ai Web)

### JWT Analysis

```bash
# Step 1: Log in to claude.ai
# Step 2: Open DevTools → Application → Cookies or Local Storage
# Find the JWT token (usually named: session, token, auth, etc.)

# Step 3: Decode it
# Online: jwt.io
# CLI: echo "YOUR.JWT.TOKEN" | cut -d. -f2 | base64 -d | jq .

# Check payload for:
{
  "user_id": "user_123",
  "email": "you@example.com",
  "role": "user",          # Can you change to "admin"?
  "exp": 1234567890,       # Expiry timestamp
  "org_id": "org_456"     # Organization ID
}
```

### JWT Attack Testing

```bash
# Attack 1: Algorithm Confusion (alg:none)
# Decode JWT → change alg to "none" → remove signature → send
# Tool: python3 -m jwt_tool YOUR_JWT -X a

# Attack 2: Algorithm Switch (RS256 → HS256)
# If server uses RS256, try switching to HS256 with public key as secret
# Tool: python3 -m jwt_tool YOUR_JWT -X s -pk public_key.pem

# Attack 3: Modify payload and resign
# Change user_id or role → re-sign with guessed/found secret
# If secret is weak → CRITICAL vulnerability

# Install jwt_tool:
pip install jwt_tool
python3 -m jwt_tool -h
```

### Session Fixation

```bash
# Test: Can you set your own session ID?
# 1. Get a session cookie before login
# 2. Log in
# 3. Check if session ID changed after login
# If NOT changed → session fixation vulnerability
```

### Cookie Security Flags

```bash
curl -I https://claude.ai/login | grep -i set-cookie

# Good response:
# Set-Cookie: session=...; HttpOnly; Secure; SameSite=Strict

# Bad response (findings):
# Missing HttpOnly → XSS can steal cookie
# Missing Secure → sent over HTTP
# Missing SameSite → CSRF possible
```

---

## OAuth Testing (if applicable)

If Anthropic uses OAuth (Google/GitHub login):

### CSRF in OAuth

```bash
# 1. Start OAuth flow → note "state" parameter
# GET /oauth/authorize?client_id=...&state=RANDOM_VALUE

# 2. Test: what if state is missing or fixed?
# GET /oauth/authorize?client_id=...  # no state
# GET /oauth/authorize?client_id=...&state=fixed_value

# If no state validation → CSRF in OAuth
```

### Open Redirect in OAuth

```bash
# Test redirect_uri manipulation
GET /oauth/authorize?
  client_id=...&
  redirect_uri=https://evil.com  # Not your registered URI

# Or URL encoding bypass:
  redirect_uri=https://claude.ai@evil.com
  redirect_uri=https://claude.ai.evil.com
  redirect_uri=https://evil.com/claude.ai

# If redirects to evil.com → code/token hijacking possible
```

---

## Authorization Testing (Privilege Escalation)

### Horizontal Privilege Escalation (Same Level)

```
Account A (regular user) accessing Account B's (regular user) data
```

```bash
# As Account B: note your user_id, conversation_ids, file_ids
# Switch to Account A's credentials
# Try to access Account B's resources

GET /api/v1/conversations/ACCOUNT_B_CONV_ID   # Using Account A's API key
GET /api/v1/account/ACCOUNT_B_USER_ID          # Profile access
```

### Vertical Privilege Escalation (Different Level)

```
Regular user → Admin access
Free tier → Paid features
```

```bash
# Try to access admin endpoints
GET /api/v1/admin/users
GET /api/v1/admin/stats
GET /api/v1/system/config

# Try to access paid features on free account
POST /api/v1/messages
{
  "model": "claude-3-opus-20240229",  # Paid model on free tier
  "max_tokens": 100000                  # Exceeding free tier limit
}
```

---

## 2FA/MFA Testing (if applicable)

```bash
# Test: TOTP brute force
# Try all 6-digit codes 000000-999999
# Is there rate limiting? Lockout after N attempts?

# Test: 2FA bypass
# 1. Start 2FA flow
# 2. Note the session/token at this stage  
# 3. Try to access authenticated endpoints directly
# 4. Can you skip 2FA entirely?

# Test: Backup codes
# Are backup codes single-use?
# Are they properly invalidated after use?
```

---

## Checklist: Auth Testing

**API Keys:**
- [ ] Empty/null key rejected
- [ ] Malformed key rejected
- [ ] Revoked key doesn't work (test immediately after revoking)
- [ ] Key doesn't appear in error messages
- [ ] Key only works in correct header

**Session (claude.ai):**
- [ ] JWT has proper expiry
- [ ] JWT alg:none attack fails
- [ ] JWT RS256→HS256 switch fails
- [ ] Session ID changes after login (no fixation)
- [ ] Cookies have HttpOnly, Secure, SameSite flags

**Authorization:**
- [ ] Can't access other users' resources (IDOR check)
- [ ] Can't access admin endpoints as regular user
- [ ] Can't use paid features on free account
- [ ] API key scoped correctly (can't do more than intended)

---

## Next Step
→ [How to Write Your Report](report-writing.md)
→ [Back to API Testing](api-testing.md)
