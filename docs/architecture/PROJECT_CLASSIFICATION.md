# Project Classification — fee-impact-calculator

| Field | Value |
|-------|-------|
| **Generated** | 2026-05-24 |
| **Method** | Auto-classified from project documentation |
| **Result** | **Tier 1 — Static/Marketing** (0/10 yes) |

## Assessment

| # | Question | Answer | Rationale |
|---|----------|--------|-----------|
| auth | User Authentication | No | No user accounts, login, or personalized data — it is a single-page Streamlit calculator with sidebar inputs only. |
| data | Data Persistence | No | No database or persisted user-generated content; all calculations are computed in-memory per session. |
| roles | Multi-Role Access | No | No user types or permissions exist since there is no auth layer. |
| integrations | Third-Party Integrations | No | Only uses local libraries (streamlit, numpy, pandas, plotly) with no external APIs or third-party services. |
| realtime | Real-Time Features | No | Streamlit reruns on input change, but there are no websockets, live data feeds, or collaborative features. |
| sensitive | Transaction Sensitivity | No | Inputs are hypothetical investment parameters, not real PII, payments, or actual financial account data. |
| scale | Scale Expectations | No | A simple educational calculator unlikely to serve >10,000 concurrent users. |
| team | Team Size | No | Tiny single-file script with no indication of multi-developer collaboration. |
| longevity | Longevity | No | Self-contained marketing/educational calculator with no roadmap suggesting 2+ years of active maintenance. |
| ai | AI/LLM Features | No | Pure deterministic compound-interest math; no AI/LLM models involved. |

## Tier Determination

**Tier 1 — Static/Marketing**

- Yes count: 0/10
- Count-based tier: 1
- Final tier: 1 (max of count + escalation)
