# Project Classification — fee-impact-calculator

| Field | Value |
|-------|-------|
| **Generated** | 2026-03-22 |
| **Method** | Auto-classified from project documentation |
| **Result** | **Tier 1 — Static/Marketing** (0/10 yes) |

## Assessment

| # | Question | Answer | Rationale |
|---|----------|--------|-----------|
| auth | User Authentication | No | This is a single-page Streamlit calculator with no user accounts or login functionality. |
| data | Data Persistence | No | All calculations are done in-memory with no database or persistent storage of user data. |
| roles | Multi-Role Access | No | There are no user roles or permissions — anyone can use the calculator equally. |
| integrations | Third-Party Integrations | No | The app uses only local Python libraries (Streamlit, NumPy, Pandas, Plotly) with no external API calls. |
| realtime | Real-Time Features | No | Streamlit re-renders on input change but there are no websockets, live data feeds, or collaborative features. |
| sensitive | Transaction Sensitivity | No | The calculator uses hypothetical inputs only — no real financial accounts, PII, or transactions are involved. |
| scale | Scale Expectations | No | This is a simple single-user calculator tool, not designed for high-concurrency or large user bases. |
| team | Team Size | No | The codebase is a single Python file, indicating a solo-developer project. |
| longevity | Longevity | No | This is a self-contained calculator with no indication of long-term maintenance or extension plans. |
| ai | AI/LLM Features | No | The app performs deterministic compound interest math with no AI or ML models involved. |

## Tier Determination

**Tier 1 — Static/Marketing**

- Yes count: 0/10
- Count-based tier: 1
- Final tier: 1 (max of count + escalation)
