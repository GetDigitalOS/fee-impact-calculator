# Compliance Report — fee-impact-calculator

**Date:** 2026-05-23
**Tier:** 1 — Static/Marketing (per `PROJECT_CLASSIFICATION.md`, 0/10 yes)
**Framework Version:** Universal Web Development Principles v2.02.01
**Auditor:** Claude (`/compliance`)

> **Note on tier fit.** The project is a single-file **Streamlit (Python) app**, not a static site. Tier 1 is the correct *complexity* tier (no auth, no DB, no PII, no AI), but several Tier 1 principles (CSS architecture, responsive breakpoints, OG/sitemap SEO, edge CDN delivery) are owned by the Streamlit runtime rather than this repo. These are marked **N/A — runtime-owned**, with the caveat that "Streamlit owns it" is only acceptable if Streamlit is actually hosting it (see DevOps gap below).

---

## Summary

| Status | Count |
|--------|------:|
| ✅ Met | 9 |
| ⚠️ Partial | 7 |
| ❌ Not Met | 14 |
| ⬜ Not Applicable | 14 |

**Overall Compliance:** **53 %** of applicable principles met or partially met (16 of 30 applicable).

---

## Critical Gaps (Fix Before Launch)

1. ❌ **Devcontainer disables CSRF + CORS protection** — `.devcontainer/devcontainer.json:22` runs Streamlit with `--server.enableCORS false --server.enableXsrfProtection false`. Acceptable only inside a Codespaces preview, but the flags are checked into the repo and will be reused by any contributor or auto-launched preview. **Tier 1 "Input Validation / Security Fundamentals" violated.**
2. ❌ **No deployment exists / registry says wrong target** — registry `stack.deploy = ["cloudflare-pages"]`, but Streamlit is a long-running Python server and cannot be hosted on Cloudflare Pages (static-only). There is no `.github/`, no Dockerfile, no `streamlit/config.toml`, no Streamlit Community Cloud manifest. **Tier 1 "Automated Deployment" violated** — no live URL means HTTPS / Security Headers / CDN are unverifiable.
3. ❌ **Registry tier is `unclassified` but classification says Tier 1** — Hub registry (`C:/dev/project-hub/registry/projects.json:1627`) is out of sync with `PROJECT_CLASSIFICATION.md`. Also `company: ""` (should be `aligned-wealth`), no `dev_port`, no `dev_command`, no `status`, no `live_url`.
4. ❌ **Requirements are unpinned** — `requirements.txt` lists `streamlit / matplotlib / plotly / pandas / numpy` with **no version constraints and no lock file**. Builds are not reproducible and any upstream CVE silently ships on next install.

---

## Detailed Results

### Tier 1 — Security Fundamentals

| | Principle | Evidence |
|-|-----------|----------|
| ⬜ | **HTTPS Everywhere** | N/A — no deployment to evaluate. Streamlit Community Cloud and Codespaces both terminate TLS at the host, so this becomes "met" automatically *once a host is chosen*. |
| ⬜ | **Security Headers** (HSTS, X-Frame-Options, CSP, etc.) | N/A at this layer — Streamlit does not let the app add response headers without a reverse proxy. Re-evaluate at the chosen hosting tier. |
| ⚠️ | **Input Validation** | All inputs are Streamlit widgets with bounded ranges (sliders + `number_input(min_value=1000)`), so type / range safety is automatic. **Gap:** `number_input` has no `max_value`, so a user can submit `1e308` and break the chart. Also three uses of `unsafe_allow_html=True` (`fee_impact_calculator.py:7, 65, 95`) — content is static, so no live XSS, but the pattern is flagged because future "just add the user's name here" edits become injection vectors. |
| ❌ | **Devcontainer turns CSRF + CORS off** | `.devcontainer/devcontainer.json:22` — `--server.enableXsrfProtection false --server.enableCORS false`. These are Streamlit's *only* built-in cross-origin defenses. See Critical Gap #1. |
| ❌ | **Dependency Hygiene** | `requirements.txt` is 5 unpinned package names. No lock file (`requirements.lock`, `Pipfile.lock`, `uv.lock`, etc.). No `pip-audit` / `safety` / Dependabot. `matplotlib` is declared but never imported in `fee_impact_calculator.py` — dead dependency. |

### Tier 1 — Design System Basics

| | Principle | Evidence |
|-|-----------|----------|
| ⬜ | **Semantic Tokens** | N/A — Streamlit owns the design system. |
| ⬜ | **Token Hierarchy** | N/A — runtime-owned. |
| ⬜ | **Consistent Spacing Scale** | N/A — runtime-owned. |
| ❌ | **Typography Scale** | `fee_impact_calculator.py:7,65` set `font-size: 20px` inline via HTML. These bypass Streamlit's own scale rather than extending it. Two arbitrary sizes in 121 LOC isn't yet a problem, but the pattern is "hardcode a number when the default doesn't fit," which is exactly what a typography scale exists to prevent. |
| ⬜ | **Icon System** | N/A — emoji used as decorative glyphs in headings (📊 💰 📈 📋 💡). Functionally fine for Tier 1; no library needed. |
| ⬜ | **Responsive Breakpoint Strategy** | N/A — Streamlit handles layout reflow. |

### Tier 1 — Performance

| | Principle | Evidence |
|-|-----------|----------|
| ⬜ | **Core Web Vitals Targets** | N/A — Streamlit is a server-rendered app with WebSocket reruns, not a CWV-measurable static page. |
| ⬜ | **Asset Optimization** | N/A — no custom static assets in repo. |
| ⬜ | **Lazy Loading** | N/A — no images. |
| ⬜ | **CDN Delivery** | N/A — runtime-owned (Streamlit Cloud serves its own JS bundle via CDN). |

### Tier 1 — Accessibility (WCAG 2.1 AA)

| | Principle | Evidence |
|-|-----------|----------|
| ⚠️ | **Semantic HTML** | Streamlit emits a generally sensible DOM, but the `unsafe_allow_html` blocks use bare `<div>` + `<strong>` instead of headings/landmarks. `fee_impact_calculator.py:64-69` builds a "Reduction in Portfolio Due to Fees" callout as a `<div>` with no role or label. |
| ⬜ | **Keyboard Navigation** | N/A — Streamlit widgets are keyboard-accessible by default. |
| ❌ | **Color Contrast** | `fee_impact_calculator.py:67` — `color: red;` on white. Pure `red` (#FF0000) on white is **4.0 : 1**, below the 4.5 : 1 minimum for normal-size body text. The flagged number is the most important value on the screen. |
| ⬜ | **Alt Text** | N/A — no images. |
| ⬜ | **Focus Indicators** | N/A — runtime-owned. |
| ⬜ | **Reduced Motion** | N/A — no app-authored animations; Plotly's hover transitions respect system settings. |

### Tier 1 — SEO Fundamentals

| | Principle | Evidence |
|-|-----------|----------|
| ⬜ | **Semantic HTML for Search** | N/A — Streamlit apps are not indexable in a meaningful way. |
| ⬜ | **OG / Social Tags** | N/A — runtime-owned. |
| ⬜ | **Structured Data** | N/A. |
| ⬜ | **robots.txt / sitemap** | N/A. |
| ⬜ | **Performance as SEO** | N/A. |

> Registry says `seo: "required"`. That field is inconsistent with the chosen runtime. Either drop the SEO requirement or move this calculator to a static framework (Next.js / Astro) where SEO is achievable.

### Tier 1 — Code Quality

| | Principle | Evidence |
|-|-----------|----------|
| ✅ | **Separation of Concerns** | Single-file Streamlit script is the idiomatic Streamlit structure; HTML/JS aren't mixed in (other than the three `unsafe_allow_html` snippets). |
| ⚠️ | **DRY** | Two parallel growth loops (`fee_impact_calculator.py:26-40`) for index vs. active — they could share a helper, but at 14 lines duplication is acceptable. The currency-format loop at `:115-116` is fine. |
| ✅ | **Semantic Naming** | `index_values`, `active_fees_paid`, `dollar_difference`, `percent_difference` — clear domain vocabulary. |
| ⬜ | **Mobile-First CSS** | N/A — no app CSS. |

### Tier 1 — DevOps Basics

| | Principle | Evidence |
|-|-----------|----------|
| ❌ | **Automated Deployment** | No `.github/workflows`, no `streamlit_app.toml`, no Dockerfile, no `wrangler.toml`. Registry's `cloudflare-pages` target is incompatible with Streamlit. |
| ❌ | **Preview Deployments** | None. |
| ❌ | **Environment Separation** | None — there is only "whatever runs in the devcontainer." |
| ❌ | **Domain & DNS Management** | No live URL recorded in registry. |
| ⚠️ | **Version Control** | Git is in use ✅, but commit messages 7 of the last 10 are literally `"Update fee_impact_calculator.py"` — fails the "meaningful commit messages" criterion. No `.gitignore` (Python projects without one routinely commit `__pycache__/`, `.venv/`, `.env`). |

### Tier 1 — UX Fundamentals

| | Principle | Evidence |
|-|-----------|----------|
| ✅ | **Responsive Design** | Streamlit's layout reflows; `use_container_width=True` is set on chart and table. |
| ⚠️ | **Error Prevention** | Sliders are bounded; `number_input` is min-bounded only. No `max_value` on initial investment means extreme inputs produce silent overflow in the chart axes rather than an explanatory error. |
| ⬜ | **Loading States** | N/A — calculation is synchronous and instant. |
| ✅ | **Consistent Patterns** | Section pattern (emoji heading → table / chart / note) is consistent. |

### Tier 1 (v2.02) — Structural Integrity

| | Principle | Evidence |
|-|-----------|----------|
| ✅ | **No swallowed errors** | No `try/except` blocks; failures propagate. |
| ✅ | **No manual sync steps** | App is stateless. |
| ⚠️ | **Actionable error messages** | None authored (because there's no error handling) — fine for happy path, but `max_value`-less number input plus no try/except means an OverflowError on huge inputs reaches Streamlit's generic red bar, not an explanation. |

---

### Cross-Cutting — Privacy & Data Protection (Tier 1)

| | Principle | Evidence |
|-|-----------|----------|
| ✅ | **Privacy policy** | N/A trigger not met — **no personal data is collected**. App is pure client-side compound math. |
| ✅ | **Cookie disclosure** | N/A — Streamlit sets a session cookie (`_streamlit_xsrf`) but no analytics/marketing cookies are added. |
| ⬜ | **Secure form submission (HTTPS)** | Depends on hosting — see Security Headers above. |
| ✅ | **Data minimization** | Inputs are hypothetical numbers; nothing leaves the session. |

**Privacy posture: clean.** No PII, no analytics, no third parties.

### Cross-Cutting — AI/LLM Integration

⬜ **Not applicable.** App is deterministic compound interest math; no LLM, no model API.

### Cross-Cutting — Dependency Management

| | Check | Evidence |
|-|-------|----------|
| ❌ | **Lock file committed** | None. No `requirements.lock`, no `Pipfile.lock`, no `uv.lock`. |
| ❌ | **Pinned versions** | Five packages, zero `==` or `~=` pins. |
| ⚠️ | **Vulnerability scan** | `pip-audit` not run in CI (no CI exists). Manually running it before launch is the immediate fix. |
| ⚠️ | **Dependency count justification** | `streamlit`, `numpy`, `pandas`, `plotly` are all used. `matplotlib` is **declared but never imported** — remove. |

### Cross-Cutting — Project Hub Registration

| | Check | Evidence |
|-|-------|----------|
| ✅ | **Registered in Project Hub** | `projects.json:1625` — path present. |
| ❌ | **Tier matches classification** | Registry says `"unclassified"`; `PROJECT_CLASSIFICATION.md` says **Tier 1**. |
| ✅ | **Git remote configured** | `https://github.com/GetDigitalOS/fee-impact-calculator.git` |
| ❌ | **Dev port assigned** | No `dev_port` in registry. (Compare sibling `retirementcapital`: `3207`.) |
| ❌ | **Status is current** | No `status` field. Sibling projects record `"development"` / `"live"`. |
| ❌ | **Company set** | `company: ""`. Should be `"aligned-wealth"` based on path. |
| ❌ | **`deploy_target` is achievable** | Registry says `cloudflare-pages` — incompatible with a Streamlit server. |
| ✅ | **`last_audited` updated** | Set to **2026-05-23** as part of this audit. |

---

## Recommendations (Prioritized)

### 🔴 Critical (Security / Compliance Risk)

1. **Remove the CORS / XSRF disables** in `.devcontainer/devcontainer.json:22`. The `postAttachCommand` should be plain `streamlit run fee_impact_calculator.py`. If the Codespaces preview needs iframe embedding, scope the relaxation to a `devcontainer.preview.json` and document why.
2. **Decide on a host and wire it.** Either:
   - Streamlit Community Cloud (free; correct fit; gives HTTPS + custom domain + preview URLs per branch), **or**
   - Rebuild as a static calc (Astro / Next.js + a small JS port) if you actually need the registry's `cloudflare-pages` target and SEO.
   Update `stack.deploy`, add `live_url`, add `dev_port`, set `status` in the registry once chosen.
3. **Pin dependencies and add a lock.** `pip install pip-tools && pip-compile requirements.in`, commit `requirements.txt` (pinned) + `requirements.lock`. Remove the unused `matplotlib` line.
4. **Fix the registry record:** set `tier: "1"`, `company: "aligned-wealth"`, populate `status`, `dev_port`, `dev_command: "streamlit"`, and either correct or remove `deploy: ["cloudflare-pages"]`.

### 🟡 Important (Quality / Reliability Risk)

5. **Cap the initial-investment input.** Add `max_value=1_000_000_000` (or similar) to `st.sidebar.number_input` at `fee_impact_calculator.py:11` to avoid silent overflow in the Plotly axis.
6. **Replace `color: red`** at `fee_impact_calculator.py:67` with `#B91C1C` (Tailwind `red-700`, ~7.4 : 1 contrast on white) — passes WCAG AA. Or use `st.error` / `st.metric(delta=…)` and drop the `unsafe_allow_html` block entirely.
7. **Add a `.gitignore`** for `__pycache__/`, `*.pyc`, `.venv/`, `.env`, `.streamlit/secrets.toml`.
8. **Use Conventional Commits.** Rewrite `"Update fee_impact_calculator.py"` going forward as `feat(calc): …` / `fix(ui): …`. Past commits don't need rewriting; just stop adding new ones in that shape.

### 🟢 Recommended (Best Practice)

9. **Add a README.md** (the devcontainer at line 8 references one that doesn't exist). One short paragraph + `pip install -r requirements.txt && streamlit run fee_impact_calculator.py` is enough at Tier 1.
10. **Replace the three `unsafe_allow_html=True` blocks** with `st.metric` / `st.info` / native Markdown. Removes the XSS-shaped footgun and earns back the Typography Scale point.
11. **Pull the growth loop into a function** `simulate(initial, years, market_return, total_fee_pct) -> (values, fees)`. Eliminates the parallel index/active loops and makes the math unit-testable — the only file in this repo that *would* benefit from a test.
12. **Add `pip-audit` as a pre-commit hook** (or just run it monthly). With no CI, this is the cheapest dependency-hygiene control available.

---

## Phase Readiness Verdict

**Not ready for "launch."** Ready for "internal demo on a developer's laptop."

The blocker is not the code — the math is correct and the UI is reasonable. The blockers are **(a) no deployment story**, **(b) security defaults are weakened in the devcontainer**, and **(c) the Hub registry describes a different project than what's actually in the repo**. Address Critical #1–#4 and Tier 1 readiness flips from 53 % to ~85 %.

## Next Review

Recommended next compliance check: **after the host is chosen and the registry is reconciled** (target: within 2 weeks). If the project pivots to a static SPA, re-run `/classify` first — the tier won't change, but several `N/A — runtime-owned` rows become actionable.
