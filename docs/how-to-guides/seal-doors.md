\# How to Seal the Doors Against Storm Surge (AI Crawl Control)



\*Goal: Protect your content from unauthorised AI scraping and model training using Cloudflare Workers.\*



\---



\## 🌊 The Analogy: Sealed Doors



During a hurricane storm surge, water can burst through ordinary doors and windows, flooding the house. \*\*Sealed doors\*\* are reinforced, watertight, and equipped with sensors to detect pressure changes.



In the AI era, your public content is like an unsealed door – AI scrapers crawl your site, download your articles, images, and data, and use them to train models without permission. This can:



\- Violate your terms of service

\- Steal your original research and creative work

\- Increase your bandwidth costs

\- Damage your SEO (if scraped content is republished elsewhere)



\*\*Sealing the doors\*\* means:



1\. \*\*Detecting AI crawlers\*\* – identify bots by user-agent, IP reputation, and behaviour.

2\. \*\*Blocking or rate‑limiting\*\* – serve a 403 or 429 instead of your content.

3\. \*\*Serving alternative content\*\* – return a “honeypot” page or a CAPTCHA challenge.

4\. \*\*Logging and alerting\*\* – record every blocked attempt for NIS2 compliance.



This guide walks you through deploying a \*\*Cloudflare Worker\*\* that acts as your sealed door – blocking or rate‑limiting known AI crawlers.



\---



\## 🧱 Prerequisites



\- A Cloudflare account (free tier works)

\- Your existing `gdpr-gatekeeper-worker` (or a new worker)

\- Familiarity with editing workers in Cloudflare Dashboard



\---



\## 📦 Option 1: Add AI Crawl Control to your existing GDPR Gatekeeper Worker



\### Step 1: Identify AI crawler user‑agents



Common AI bot user‑agents (as of 2026):



| Bot | User‑agent string (partial) |

|-----|----------------------------|

| OpenAI GPTBot | `GPTBot` |

| Google Bard / Gemini | `Google-Extended` |

| Anthropic Claude | `ClaudeBot` |

| Meta AI | `meta-externalagent` |

| Amazon Alexa | `AlexaSiteInfo` |

| Common crawl | `CCBot` |

| Bytespider (TikTok) | `Bytespider` |

| AppleBot | `Applebot` (optional) |

| Facebook external hit | `facebookexternalhit` (optional) |



You can also use Cloudflare’s \*\*Bot Management\*\* (paid) or a community maintained list (e.g., from `ai.txt` initiatives).



\### Step 2: Add detection and blocking to your worker



Open your `gdpr-gatekeeper-worker` in Cloudflare Dashboard → \*\*Edit code\*\*.



Inside the `handleRequest` function, \*\*at the very top\*\* (before consent check), add:



```javascript

&#x20; // --- AI Crawl Control (seal the doors) ---

&#x20; const userAgent = request.headers.get('User-Agent') || '';

&#x20; const aiBots = \['GPTBot', 'Google-Extended', 'ClaudeBot', 'meta-externalagent', 'CCBot', 'Bytespider'];

&#x20; const isAiBot = aiBots.some(bot => userAgent.includes(bot));



&#x20; if (isAiBot) {

&#x20;   await AUDIT\_KV.put(`ai\_block:${Date.now()}`, JSON.stringify({ ua: userAgent, ip: userIp, path: url.pathname }));

&#x20;   return new Response(JSON.stringify({ error: 'AI crawlers are not allowed. See /robots.txt' }), { status: 403, headers: { 'Content-Type': 'application/json' } });

&#x20; }

Placement example:

async function handleRequest(request) {

&#x20; const url = new URL(request.url);

&#x20; const userIp = request.headers.get('CF-Connecting-IP') || 'unknown';

&#x20; const userAgent = request.headers.get('User-Agent') || '';



&#x20; // AI Crawl Control – block known bots

&#x20; const aiBots = \['GPTBot', 'Google-Extended', 'ClaudeBot', 'meta-externalagent', 'CCBot', 'Bytespider'];

&#x20; if (aiBots.some(bot => userAgent.includes(bot))) {

&#x20;   await AUDIT\_KV.put(`ai\_block:${Date.now()}`, JSON.stringify({ ua: userAgent, ip: userIp, path: url.pathname }));

&#x20;   return new Response(JSON.stringify({ error: 'AI crawlers are not allowed.' }), { status: 403 });

&#x20; }



&#x20; // ... rest of your worker (consent check, etc.)

}

Step 3: Save and deploy

Click Save and Deploy.



Step 4: Test with a fake AI user‑agent

Use curl to simulate a GPTBot:



curl -A "GPTBot" https://gdpr-gatekeeper-worker.william-897.workers.dev/

You should get a 403 with the error message.

📊 Option 2: Create a dedicated AI Crawl Control Worker

If you prefer a separate worker (e.g., as a reverse proxy in front of your static site), use this standalone code:

// AI Crawl Control Worker – standalone

addEventListener('fetch', event => {

&#x20; event.respondWith(handleRequest(event.request));

});



async function handleRequest(request) {

&#x20; const url = new URL(request.url);

&#x20; const userAgent = request.headers.get('User-Agent') || '';

&#x20; const aiBots = \['GPTBot', 'Google-Extended', 'ClaudeBot', 'meta-externalagent', 'CCBot', 'Bytespider'];



&#x20; if (aiBots.some(bot => userAgent.includes(bot))) {

&#x20;   // Optionally log to KV (need to bind AUDIT\_KV)

&#x20;   return new Response('AI crawlers are blocked', { status: 403 });

&#x20; }



&#x20; // Otherwise, proxy the request to your origin

&#x20; return fetch(request);

}

Deploy it and then route your domain’s traffic through this worker.

🧪 Advanced: Rate‑limiting for aggressive scrapers

Even non‑bot user‑agents can scrape aggressively. Add a rate limiter (using KV) to block any IP that exceeds a threshold.

// Rate limiting for any request (example using in‑memory Map, but better with KV)

const rateMap = new Map(); // in production use KV



async function isRateLimited(ip) {

&#x20; const now = Date.now();

&#x20; const window = 60000; // 1 minute

&#x20; const limit = 60; // 60 requests per minute

&#x20; const record = rateMap.get(ip) || \[];

&#x20; const recent = record.filter(t => now - t < window);

&#x20; if (recent.length >= limit) return true;

&#x20; recent.push(now);

&#x20; rateMap.set(ip, recent);

&#x20; return false;

}

Then call

if (await isRateLimited(userIp)) return new Response('Too many requests', { status: 429 });



📊 Comparison: Checkbox vs. Resilience – AI Crawl Control

Checkbox Approach	Sealed Doors (This Worker)

robots.txt disallowing AI bots	Enforced at the edge – bots cannot ignore robots.txt

No logging of scraping attempts	Every block logged to AUDIT\_KV with user‑agent and IP

No rate limiting	Optional per‑IP rate limiting to stop aggressive scrapers

Serves full content to all	Can return a minimal “honeypot” or CAPTCHA page

Compliance proof = robots.txt file	Live logs showing blocked requests and patterns

🧠 Reflection Questions

How do you know if someone is scraping your site with a custom script that uses a common browser user‑agent?

Resilient answer: you use rate limiting and behaviour analysis (e.g., request frequency, time between requests).



Can you produce a report of all AI crawler attempts in the last 30 days?

Resilient answer: Yes – your AUDIT\_KV has keys starting with ai\_block: containing the details.



What happens when a new AI bot appears with an unknown user‑agent?

Resilient answer: you update the aiBots array and redeploy. For proactive defence, you can block all user‑agents containing bot, crawler, scraper, etc. (with a risk of false positives).



💡 Sealing the doors doesn’t stop the storm, but it keeps the flood out. Your content stays yours.



🔗 Next Steps

Combine with NIS2 Incident Logger to send alerts when a known AI bot is blocked.



Publish an ai.txt or updated robots.txt to signal your blocking policy.



Read the Explanation: Principles of Structural Engineering for more on layered defence.



\---



\## 📝 How to save and push



In \*\*Command Prompt\*\* (from your project root):



```cmd

notepad docs\\how-to-guides\\seal-doors.md



