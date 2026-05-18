# How to Get a GDPR Consent Token (for API Access)

To use the hurricane‑proof API (e.g., accessing eSIM comparison data), you need a valid consent token. This token is issued only to **GitHub sponsors** of the [Mobile Security Guru](https://github.com/sponsors/mobile-security-guru) project.

## 1. Sponsor the project

- Visit [GitHub Sponsors](https://github.com/sponsors/mobile-security-guru)
- Choose a tier (any amount works)
- Complete the sponsorship (you can cancel later if needed)

## 2. Obtain your consent token

Send a `POST` request to the GDPR Gatekeeper Worker with your GitHub username:

```bash
curl -X POST https://gdpr-gatekeeper-worker.william-897.workers.dev/consent \
  -H "Content-Type: application/json" \
  -d '{"github": "YOUR_GITHUB_USERNAME"}'