# Idea submission: Registration & Idea Phase

**Track:** Starter Kit 01, Treasury & Ops: Adaptive Treasury Controller

## Title

Ballast: an Adaptive Treasury Controller that rehearses every decision before it moves money

## Description

**The problem**
When cash is short, finance teams decide in spreadsheets and Slack threads which bills to pay, which currencies to convert, and what can wait. They decide late, with no view of risk, and often pay FX spreads and late fees they could have avoided. Today's automation follows fixed rules. It cannot reason about uncertainty, and it should not be trusted to move money on its own.

**What Ballast does**
Ballast is an agent that runs on an Airwallex account. Every morning, and whenever something changes, it chooses one of four actions for each upcoming obligation: **Fund**, **Convert**, **Defer**, or **Escalate**. Every action stays inside a spending mandate that a human signed.

**How it works (a six-step loop)**
1. **Sense:** reads multi-currency balances, incoming funds, scheduled payouts, beneficiaries, and live FX quotes through Airwallex APIs (Balances, FX Quotes & Conversions, Transfers, Webhooks), plus the payables and receivables lists.
2. **Forecast:** builds a 13-week cash forecast per currency with calibrated uncertainty bands (conformal prediction). Saying "90% likely between X and Y" is more useful than one wrong number.
3. **Plan:** solves for the cheapest plan whose shortfall risk (CVaR) stays under the company's risk budget. It weighs late fees, early-payment discounts, FX spread, and vendor criticality. Claude turns the plan into a short, readable rationale for each decision.
4. **Rehearse:** before executing, it replays the plan against 1,000 simulated futures on a shadow ledger, such as a customer paying 9 days late or EUR moving 3%. If the chance of a breach is above the limit, it re-plans or escalates.
5. **Act within the mandate:** executes conversions with a locked FX quote and makes payouts with idempotency keys, so the same bill can never be paid twice. Anything outside the mandate (amount, counterparty, currency, timing) goes to a human for one-tap approval. The agent can never extend its own mandate.
6. **Learn:** compares forecasts with what actually happened and recalibrates. Vendors and customers that are often late get wider bands.

**Example scenario for the demo**
Balances: USD 182k, EUR 41k, SGD 96k. Coming up: SGD 118k payroll on Monday, a €64k supplier invoice (2% discount if paid by Tuesday), USD 38k cloud bill, USD 55k tax payment. A USD 140k customer receipt is expected Tuesday, but this customer is late 38% of the time.
Ballast's plan:
- **Fund** payroll by converting USD to SGD now at a locked quote.
- **Defer** the supplier payment, with a condition: take the 2% discount only if the receipt lands by Tuesday 10:00; otherwise pay on the due date.
- **Convert** EUR only when it is needed.
- **Escalate** the tax payment to the CFO, because paying it early would push the risk of a shortfall over budget.
Every decision shows its cost, its risk, and the reason behind it.

**Why it's different (based on recent research)**
- **Calibrated risk instead of point guesses:** builds on conformal risk control, which has recently been extended to CVaR-style risk measures (arXiv 2510.08748).
- **Mandate-bound autonomy:** follows the 2026 direction on verifiable agent authority: GLEIF's Agent Mandate Credentials (Aug 2026) and the Verifiable Intent spec from Mastercard and Google (Mar 2026). Each action carries proof of who authorised it and within what limits.
- **Prompt-injection safe by design:** invoice PDFs and emails are untrusted data. Following the CaMeL pattern (Google DeepMind, 2025), they can fill in values but can never change what the agent decides to do. A malicious invoice cannot redirect a payment.
- **No double payments:** idempotency keys plus ledger reconciliation through webhooks before any retry.

**What judges will see**
A live cockpit with a cash runway chart and its uncertainty bands, the queue of obligations with Fund / Convert / Defer / Escalate decisions, a "rehearsal" view showing simulated futures, a timeline of Airwallex API calls, and a "what if" panel. In that panel you can inject a shock (late customer, FX move, surprise bill) and watch the agent re-plan in seconds.

**Build plan (Oct 27 to Nov 13)**
Week 1: connect to the Airwallex sandbox, build the forecaster and the shadow ledger. Week 2: build the planner, mandate engine, and the Claude reasoning layer. Week 3: build the cockpit UI, the shock scenarios, and the demo video.

**Impact**
Lower FX and late-fee costs, fewer cash crunches, and an AI agent finance teams can trust, because it explains every decision, proves its risk before acting, and never goes beyond what a human authorised.
