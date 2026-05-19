# GDPR Gatekeeper Worker API Reference

*Technical specifications for the impact‑resistant windows (GDPR consent and data residency) Cloudflare Worker.*

---

## 🧭 Base URL
https://gdpr-gatekeeper-worker.william-897.workers.dev

(Replace with your deployed worker’s subdomain.)

---

## 🔐 Authentication

Most endpoints require a valid consent token in the `X-Consent-Token` header.  
The token is obtained from the `POST /consent` endpoint (see below).

**Example header:**
X-Consent-Token: 550e8400-e29b-41d4-a716-446655440000


If the token is missing, expired, or invalid, the API returns `403 Forbidden`.

---

## 📋 Endpoints

### `POST /consent`

Obtain a consent token. This endpoint does **not** require an existing token.

**Request body** (JSON):

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `github` | string | Yes | The user’s GitHub username (must be a sponsor of the project) |

**Example request** (Command Prompt):

```cmd
curl -X POST https://gdpr-gatekeeper-worker.william-897.workers.dev/consent -H "Content-Type: application/json" -d "{\"github\":\"mobile-security-guru\"}"
Success response (200 OK):
{
  "token": "550e8400-e29b-41d4-a716-446655440000",
  "message": "Consent recorded"
}
Error responses:
Status	Body	Cause
400	{"error":"Please provide your GitHub username: {\"github\":\"username\"}"}	Missing github field
403	{"error":"You must be a GitHub sponsor to obtain a consent token"}	User is not a sponsor
500	{"error":"KV bindings missing"}	Worker misconfiguration (contact admin)
GET / (or any endpoint except /consent and /eu-only/*)
General API endpoint. Requires a valid consent token.

Request headers:

X-Consent-Token – required, obtained from POST /consent

Example request:
curl -H "X-Consent-Token: 550e8400-e29b-41d4-a716-446655440000" https://gdpr-gatekeeper-worker.william-897.workers.dev/
Success response (200 OK):
{
  "message": "Hurricane‑proof API",
  "consent": true
}
Error responses:

Status	Body	Cause
403	{"error":"Consent required. Please POST to /consent"}	Missing or invalid token
GET /eu-only/data (optional EU‑restricted data endpoint)
Returns data that is only accessible from within the European Union.
Requires a valid consent token and the request’s IP country must be an EU member state.

Request headers:

X-Consent-Token – required

Example request:
curl -H "X-Consent-Token: YOUR_TOKEN" https://gdpr-gatekeeper-worker.william-897.workers.dev/eu-only/data
Success response (200 OK, only if from EU IP):
{
  "message": "This is EU‑protected data"
}
Error responses:

Status	Body	Cause
403	{"error":"This data is only available within the EU"}	Request from non‑EU country
403	{"error":"Consent required. Please POST to /consent"}	Missing or invalid token
🗄️ KV Namespace Schemas
The worker uses two KV namespaces. Bind them as CONSENT_KV and AUDIT_KV.

CONSENT_KV – Consent tokens and sponsor cache
Key pattern	Value	TTL	Description
<token>	"true"	365 days	Valid consent token
sponsor:<github_username>	"true" or "false"	1 hour	Cache of GitHub sponsorship status
AUDIT_KV – Audit logs (NIS2 compliant)
Key pattern	Value	TTL	Description
access:<timestamp>	JSON string with ip, path, timestamp	None (manual cleanup)	Log of every successful request
denied:<timestamp>	JSON string with ip, path, reason	None	Log of requests without valid token
residency_block:<timestamp>	JSON string with ip, country, path	None	Log of requests blocked due to non‑EU origin
Example audit record (denied):
{
  "ip": "192.168.1.1",
  "path": "/",
  "reason": "no_consent"
}
⚠️ Error Codes Summary
HTTP Status	Meaning	When it occurs
200	OK	Request successful
400	Bad Request	Missing required field in body (e.g., github)
403	Forbidden	Missing/invalid token, not a sponsor, or non‑EU access to restricted endpoint
404	Not Found	Endpoint does not exist (e.g., /invalid)
429	Too Many Requests	(If intrusion detection added) Too many attempts without token
500	Internal Server Error	Worker configuration issue (e.g., KV binding missing)
🧪 Testing with curl (Command Prompt)
Get a token (you must be a sponsor):
curl -X POST https://gdpr-gatekeeper-worker.william-897.workers.dev/consent -H "Content-Type: application/json" -d "{\"github\":\"YOUR_GITHUB_USERNAME\"}"
Store token in environment variable (optional, for easier testing):

set TOKEN=550e8400-e29b-41d4-a716-446655440000
Call main endpoint:
curl -H "X-Consent-Token: %TOKEN%" https://gdpr-gatekeeper-worker.william-897.workers.dev/
Call EU‑only endpoint (from EU IP):
curl -H "X-Consent-Token: %TOKEN%" https://gdpr-gatekeeper-worker.william-897.workers.dev/eu-only/data
🔧 Worker Configuration (for administrators)
Required bindings and secrets:

Binding type	Variable name	Value / description
KV Namespace	CONSENT_KV	Namespace for consent tokens
KV Namespace	AUDIT_KV	Namespace for audit logs
Secret	GITHUB_TOKEN	GitHub personal access token (if using sponsorship check)
(Optional)	API_KEY	For NIS2 incident logger integration (not used by this worker)
The worker code is written in JavaScript (Service Worker syntax) and deployed via Cloudflare Dashboard or Wrangler.
📚 Related Documentation
Tutorial: The Deep Foundation – how the KV stores work

Tutorial: Impact‑Resistant Windows – GDPR consent and data residency

How‑To Guide: Get a Consent Token – step‑by‑step for users

Explanation: Why a Solid Foundation Matters – compliance vs. resilience
*Last updated: 2026-05-19*