# \# Impact‑Resistant Windows: Data Residency \& GDPR

# 

# \*Tutorial objective: Learn how to install impact‑resistant windows – GDPR consent management, data residency enforcement, and privacy‑by‑design – using Cloudflare Workers and KV.\*

# 

# \---

# 

# \## 🪟 The Analogy: Why Windows Matter

# 

# After a deep foundation and a reinforced roof, the next vulnerability in a hurricane‑proof house is the windows. Ordinary windows shatter when debris hits, letting the storm inside. \*\*Impact‑resistant windows\*\* are laminated with a strong interlayer; they may crack but don’t break, keeping the house sealed.

# 

# In cybersecurity, “windows” are your data exchange points – APIs, forms, consent dialogs. GDPR requires:

# 

# \- \*\*Clear consent\*\* before collecting personal data (like a window that only opens when you turn the handle).

# \- \*\*Data residency\*\* – EU user data must stay in the EU (like a window that only opens inward, not outward).

# \- \*\*Right to be forgotten\*\* – delete user data on request (close the window permanently).

# 

# This tutorial shows you how to build impact‑resistant windows using the \*\*GDPR Gatekeeper Worker\*\* you already deployed (or will deploy). We'll focus on three layers:

# 

# 1\. \*\*Consent collection \& validation\*\* – the handle.

# 2\. \*\*Data residency enforcement\*\* – the laminated glass.

# 3\. \*\*Audit and deletion\*\* – the window frame that records every opening and closing.

# 

# \---

# 

# \## 🧱 Layer 1: Consent Collection – The Handle

# 

# A checkbox approach: “We have a cookie banner that stores consent in localStorage.”  

# Resilience: “Consent is stored server‑side in KV, linked to a cryptographically random token, and can be revoked at any time.”

# 

# \### How it works in your GDPR Gatekeeper Worker

# 

# Your existing worker already implements this:

# 

# \- `POST /consent` – creates a token and stores `"true"` in `CONSENT\_KV`.

# \- `X-Consent-Token` header – required for all other endpoints.

# \- No token → 403 error.

# 

# \*\*Why this is resilient:\*\*

# \- Consent is not stored in a cookie that can be tampered with.

# \- Token is long‑lived but can be revoked immediately by deleting the KV entry.

# \- Audit logs record each consent grant and denial.

# 

# \### Enhancing consent with user details

# 

# You can extend the `/consent` endpoint to store not just `"true"` but also the user’s email, country, and consent timestamp. Example modification:

# 

# ```javascript

# // Inside POST /consent block

# const body = await request.json();

# const userEmail = body.email || null;

# const userCountry = body.country || null;

# const consentRecord = {

# &#x20; granted: true,

# &#x20; email: userEmail,

# &#x20; country: userCountry,

# &#x20; timestamp: Date.now(),

# &#x20; token: newToken

# };

# await CONSENT\_KV.put(newToken, JSON.stringify(consentRecord), { expirationTtl: 86400 \* 365 });

