\# The History of Compliance Codes \& Their Limits



\*An explanation of how building codes (and compliance standards) evolved – and why they are necessary but not sufficient for true resilience.\*



\---



\## 🧱 The Origins of Building Codes



After the Great Fire of London in 1666, the city introduced some of the first modern building codes. These required:



\- Brick or stone construction (instead of wood)

\- Minimum wall thicknesses

\- Fire breaks between buildings



The codes worked. Fires became less catastrophic. But they were \*\*reactive\*\* – written after a disaster, based on what failed last time. And they set a \*\*minimum\*\* standard. A house could meet every code and still collapse in a hurricane, because the codes didn’t consider wind uplift or storm surge.



\*\*Compliance standards (GDPR, NIS2, ISO 27001) follow the same pattern.\*\* They codify lessons from past breaches, but they are always one step behind attackers. They tell you the minimum you must do – not how to survive an unknown threat.



\---



\## 📜 The Evolution of Compliance in Cybersecurity



| Era | Trigger | Standard | What It Missed |

|-----|---------|----------|----------------|

| 1990s | Morris worm, early hacks | ISO 17799 (later 27001) | Network perimeter thinking; ignored insider threats |

| 2000s | Sarbanes‑Oxley (Enron) | SOX IT controls | Focused on financial data, not customer privacy |

| 2010s | Massive data breaches (Target, Equifax) | PCI DSS, HIPAA, GDPR | Reactive; checklists didn’t prevent novel attacks |

| 2020s | SolarWinds, Log4j, AI scraping | NIS2, DORA, AI Act | Still point‑in‑time; can’t keep pace with AI‑driven threats |



Each new standard raises the baseline, but attackers adapt faster. \*\*Compliance is a rear‑view mirror.\*\*



\---



\## 🏗️ The Problem with Minimum Standards



Imagine two houses:



\- \*\*House A\*\*: Built exactly to code – the cheapest materials, the minimum stud spacing, the thinnest drywall. It passes inspection.

\- \*\*House B\*\*: Engineered for resilience – deeper foundation, hurricane straps, impact windows, redundant drainage. It exceeds code in every dimension.



When a Category 4 hurricane hits, House A collapses. House B stands. The building inspector shrugs: “House A met code.”



\*\*In cybersecurity, “compliant” does not mean “secure.”\*\* Many organisations that passed a SOC2 or ISO 27001 audit have suffered major breaches. The audit verified that they had a policy – not that the policy was effective against a determined adversary.



\---



\## 🔁 Why Compliance Lags Reality



1\. \*\*Regulations are slow\*\* – It takes years to draft, approve, and mandate a new standard. Attackers innovate in days.

2\. \*\*Audits are point‑in‑time\*\* – A certificate is valid for a year. Your security posture changes every hour.

3\. \*\*Checklists are linear\*\* – Real attacks are multi‑faceted. They exploit the gap between controls, not a single missing checkbox.

4\. \*\*Compliance measures activity, not outcome\*\* – You can log everything but never review the logs. You can have a firewall but misconfigure it.



NIS2 tries to address this by requiring incident reporting and continuous improvement, but it still leaves room for checkbox interpretation.



\---



\## 🌪️ The Hurricane‑Proof House as a Standard



What if building codes were rewritten to focus on \*\*demonstrable resilience\*\* instead of material checklists?



\- \*\*Not\*\*: “You must install hurricane straps.”  

&#x20; \*\*Instead\*\*: “Your house must survive a 150mph wind load for 10 minutes. Demonstrate it via engineering simulation or wind tunnel test.”



\*\*For cybersecurity, that means:\*\*



\- \*\*Not\*\*: “You must have an asset inventory.”  

&#x20; \*\*Instead\*\*: “Your system must be able to list every asset within 5 seconds of request, with last‑updated timestamp.”

\- \*\*Not\*\*: “You must log access attempts.”  

&#x20; \*\*Instead\*\*: “Your system must produce a tamper‑proof audit trail for the last 90 days on demand, with <2 second latency.”

\- \*\*Not\*\*: “You must have a data retention policy.”  

&#x20; \*\*Instead\*\*: “Your system must automatically delete personal data after 30 days of inactivity, and you must be able to prove deletion.”



The hurricane‑proof house toolkit (Workers + KV) provides the \*\*evidence\*\*. The code is the policy. The logs are the proof.



\---



\## 📊 What a Resilience‑Focused Standard Might Look Like



| Compliance Requirement | Current Checkbox | Resilience‑Focused Alternative |

|------------------------|------------------|--------------------------------|

| Access control | “We have a password policy.” | “Every API request is gated by a policy‑as‑code worker that verifies GitHub sponsorship and logs every access.” |

| Audit logging | “We enable logging on critical systems.” | “We store immutable audit logs in geo‑replicated KV, with retention of 90 days and API queryable by timestamp.” |

| Data residency | “Our data centres are in the EU.” | “Our edge worker blocks non‑EU access to EU‑only endpoints, and we log every residency violation.” |

| Incident response | “We have a written plan.” | “We run automated incident detection (intrusion detection snippet) and can produce a log of all incidents in under 1 second.” |



\---



\## 🧠 For Legal \& Risk Teams



When a regulator asks, “How do you comply with Article 32 of GDPR (security of processing)?”, a checkbox answer is: “We have a policy.” A resilience answer is: “Here is our live audit log of all access. Here is our edge‑blocking logic. Here is our consent token issuance flow.”



The latter provides \*\*demonstrable evidence\*\*. It shifts the conversation from “trust us” to “verify for yourself.”



\---



\## 🔚 The Limits of Compliance



Compliance is the \*\*floor\*\*, not the ceiling. It is necessary for legal protection, but it is insufficient for true resilience. The organisations that survive major attacks are those that:



\- Go beyond checklists

\- Engineer continuous verification

\- Instrument everything

\- Practice incident response with real data



The hurricane‑proof house is not built to code – it is built to \*\*survive the storm\*\*. Your compliance programme should be the same.



\---



\## 🔗 Next Reading



\- \[Why a Solid Foundation Matters](foundation-why.md) – deeper dive into checkbox vs. resilience

\- \[The Deep Foundation tutorial](../tutorials/foundation.md) – how to implement asset tracking and identity gatekeeping

\- \[Principles of Structural Engineering for Cyber Storms](resilience-principles.md) – how to design layered defence



