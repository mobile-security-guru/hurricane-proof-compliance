\# The Reinforced Roof: Endpoint \& API Protection



\*Tutorial objective: Learn how to add a reinforced roof – rate limiting, intrusion detection, and API security – to your hurricane‑proof house using Cloudflare Workers.\*



\---



\## 🧱 The Analogy: Why a Roof Matters



After a deep foundation, the next layer of a hurricane‑proof house is the \*\*reinforced roof\*\*. A weak roof will tear off in high winds, exposing everything inside.



\- \*\*Checkbox thinking:\*\* “We have a firewall and an API key.” (Like a thin sheet of plywood nailed down.)

\- \*\*Resilience:\*\* A roof strapped to the foundation, with shingles designed to resist uplift, and sensors that detect the first loose tile. (Rate limiting, anomaly detection, and real‑time alerting.)



In our toolkit, the roof is made of:



1\. \*\*Rate limiting\*\* – prevents a single attacker from overwhelming your API.

2\. \*\*Intrusion detection\*\* – logs suspicious patterns (e.g., repeated failed logins).

3\. \*\*API security\*\* – validates requests, blocks malicious payloads, and adds a Web Application Firewall (WAF).



\---



\## 🏗️ Component 1: Rate Limiting – The Shingles



A checkbox says: “We have a rate limit of 100 requests per minute.”  

Resilience says: “The limit is adaptive – stricter during storms, and we notify you when a client hits the limit.”



\### How to Build with Cloudflare Workers + KV



Use the same `AUDIT\_KV` namespace to track request counts per IP.



\*\*Worker skeleton for rate limiting:\*\*



```javascript

// Rate limiting middleware

async function rateLimit(ip, env, limit = 100, window = 60) {

&#x20; const key = `rate:${ip}`;

&#x20; const current = await env.RATE\_KV.get(key);

&#x20; const now = Math.floor(Date.now() / 1000);

&#x20; let requests = current ? JSON.parse(current) : \[];



&#x20; // Remove entries outside the window

&#x20; requests = requests.filter(timestamp => now - timestamp < window);

&#x20; if (requests.length >= limit) {

&#x20;   await env.AUDIT\_KV.put(`rate\_block:${now}`, JSON.stringify({ ip, requests }));

&#x20;   return false;

&#x20; }

&#x20; requests.push(now);

&#x20; await env.RATE\_KV.put(key, JSON.stringify(requests), { expirationTtl: window });

&#x20; return true;

}

