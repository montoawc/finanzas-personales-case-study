# Product Decisions

[← Back to README](../README.en.md)

Every major decision was documented with what was rejected and the risk accepted. When the scope document and a decision contradicted each other, it was flagged and resolved explicitly, never silently by the AI assistant.

| Topic | Decision | Alternatives rejected | Why | Accepted risk |
|---|---|---|---|---|
| **Backend** | Supabase (Postgres, Auth, RLS, Storage) | Shared hosting (typically MySQL/PHP, no RLS or Auth) · Internet Computer (requires redesigning the schema, no SQL) | Out of the box: per-user isolation, private storage, migrations and backups | Free-plan limits (pauses on inactivity); paid plan under evaluation |
| **UI build approach** | Claude Code only (React + Vite) | Low-code tool | Control over code, tests and architecture | More upfront work |
| **Installments** | Full expense at purchase; installments are **cash commitments** | Expense per installment (chosen first, then reverted) | Avoids double counting as long as the model guarantees it; matches the scope document | Budgets need their own criterion (below) |
| **Budgets** | Mixed criterion: regular purchases by purchase date, installments in the month they fall due | A) by recognition, as in the document · C) everything by payment month | Mirrors how a monthly budget is actually lived | Two different numbers in the same month (period result vs. budget consumption); the UI explains it. Reverting to A means changing a single function, no data migration |
| **Alerts** | One per category, month and threshold (50/75/90/100 %) | Re-alerting every time a threshold is crossed again | Avoids alert fatigue | In-app only; no push or email |
| **Reserves** | The user **confirms** how much to deduct when the real expense is paid | Automatic deduction | Prevents wrong deductions in a case with many variants | One extra manual step |
| **Rebates** | Pending ≠ available; when received, recorded as income linked to the original expense | Subtracting the rebate from the expense | Honest balances: money that hasn't arrived isn't counted | One extra category (savings/rebate) to avoid mixing concepts |
| **USD** | Buy/sell dollars as an explicit operation with the **two real amounts**; the exchange rate is calculated | Implicit conversion · manually chosen rate | No ambiguity or rounding; mirrors the real flow (USD spend → buy dollars → pay the statement) | Only links ARS with USD; no fees or taxes |
| **Balances** | Always labeled **calculated / reconciled / estimated** | A single "real balance" | Never claim something not verified against the bank | "You can spend" needs a reconciled account |
| **Deletion** | Soft-delete with audit trail; one exception: "Reset data" with a typed phrase and recent password | Free deletion | Accounting traceability | The exception was explicitly approved |
| **Test environment** | Cloud development project with synthetic data | Local Supabase with Docker | The machine had no virtualization support | No native `supabase test db`; solved with a custom script |
| **Out of scope** | Bank connection, OCR, scraping, payments and financial advice | — | Accounting reliability over feature count | Manual data entry |

## Where I changed my mind

- **Installments:** I first chose "expense per installment". After seeing the model can avoid double counting, I went back to "full expense at purchase", which is closer to real accounting.
- **Budgets:** the original document prescribed one criterion; I evaluated three and chose the mixed one, documenting its risks instead of hiding them.
- **My own mistake:** a security migration reopened permissions that were already closed. I caught it with Supabase's security advisories, fixed it and added a test that guards it.
