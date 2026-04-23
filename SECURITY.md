# Security Policy

## Supported Versions

This is a personal portfolio/resume site. Security fixes are applied to the
`master` branch on a best-effort basis.

## Reporting a Vulnerability

If you discover a security vulnerability, please **do not** open a public
GitHub issue. Instead, email directly:

**tasib.haider&#64;gmail.com**

Please include:
- A description of the vulnerability and its potential impact
- Steps to reproduce or proof-of-concept
- Any suggested remediation if known

You can expect an acknowledgement within **72 hours** and a resolution or
status update within **14 days**.

## Known Intentional Disclosures

The following items are visible in the page source by design — they are
**not** vulnerabilities:

| Item | Reason |
|---|---|
| Google Analytics Tracking ID (`UA-38723539-4`) | Client-side GA requires the ID in page source. Protect your GA property via **domain filtering** in the Google Analytics admin panel, not by hiding the ID. |
| Google AdSense Publisher ID (`ca-pub-9986369880975389`) | AdSense publisher IDs are public-facing by design. Fraud protection is handled by Google's systems. Monitor via the AdSense dashboard for invalid traffic. |

## Security Measures in Place

### In `index.html` (meta tags)

| Header | Value | Purpose |
|---|---|---|
| `Content-Security-Policy` | Allowlisted origins only | Blocks XSS, restricts resource origins |
| `X-Content-Type-Options` | `nosniff` | Prevents MIME-type sniffing attacks |
| `Referrer-Policy` | `strict-origin-when-cross-origin` | Limits referrer data sent to third parties |
| `Permissions-Policy` | `camera=(), microphone=(), geolocation=()` | Disables unused browser APIs |

### In HTML
- **`rel="noopener noreferrer"`** on all `target="_blank"` links — prevents reverse tabnapping
- **YouTube privacy-enhanced mode** — embeds use `youtube-nocookie.com`
- **`sandbox` attribute** on all iframes — restricts iframe capabilities
- **Email obfuscation** — HTML entity-encoded to deter spam bot harvesting
- **HTTPS-only links** — all external links use `https://`

### Dependencies
- Tracked in `package.json`; run `npm audit` regularly
- CI runs `npm audit --audit-level=high` on every push via `.travis.yml`

## Server-Side Headers (GitHub Pages)

> ⚠️ GitHub Pages does not support custom HTTP response headers natively.
> The `Content-Security-Policy` and other security policies above are
> delivered via `<meta>` tags as a best-effort fallback.
>
> For full HTTP header control, consider one of:
> - **Cloudflare** (free tier) — add as a proxy in front of GitHub Pages,
>   use Transform Rules to inject response headers
> - **Netlify** — supports `_headers` file for per-path response headers
> - **Vercel** — supports `vercel.json` `headers` configuration

The following headers **cannot** be set via meta tags and require a
server/CDN layer:

| Header | Recommended Value | Why it matters |
|---|---|---|
| `X-Frame-Options` | `SAMEORIGIN` | Clickjacking protection (backup to CSP `frame-ancestors`) |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | Forces HTTPS, prevents SSL stripping |
| `Cross-Origin-Opener-Policy` | `same-origin` | Isolates browsing context, mitigates Spectre |
| `Cross-Origin-Resource-Policy` | `same-origin` | Prevents cross-origin resource embedding |

## Dependency Vulnerabilities

Run `npm audit` locally to check for known CVEs in the dependency tree.

```bash
npm run audit        # High/critical only (matches CI gate)
npm audit            # Full report
npm audit fix        # Apply safe automatic fixes
npm audit fix --force  # Apply all fixes (may include breaking changes)
```
