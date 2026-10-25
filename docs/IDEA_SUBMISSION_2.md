# Idea submission 2: Registration & Idea Phase

**Track:** Starter Kit 03, Treasury & Ops: Payment Ops Incident Commander

## Title

PayOnce: an AI incident commander that fixes stuck supplier payments without ever paying twice

## Description

THE PROBLEM
A supplier emails: "We still haven't received your payment. We stop shipping on Monday." The ops team panics. Some teams resend the money and later find the first transfer also arrived, so they have paid twice. Others wait too long and the supply chain stops. Worst of all, fraudsters exploit exactly this moment: "Payment failed? Please use our new bank account." Most business email compromise (BEC) losses start with urgency like this.

THE SOLUTION
PayOnce is an AI agent that treats every delayed payment like a production incident. It opens an incident, collects evidence, finds the root cause, picks the safest fix, and keeps the supplier updated. It does all this under one rule it can never break: the total paid on an invoice can never exceed the invoice amount.

HOW IT WORKS
1. Detect: watches Airwallex transfer statuses and webhooks, and reads supplier emails. A transfer that is late against its expected settlement time automatically opens an incident.
2. Investigate: gathers the evidence in one timeline: transfer status and history, beneficiary details, payment rail, cut-off times, bank holidays, compliance checks, and balance movements.
3. Diagnose: Claude reasons over the evidence and ranks likely causes with confidence levels, for example "sent after cut-off + local bank holiday (82%)" vs "beneficiary name mismatch (12%)".
4. Decide: picks one action: WAIT (with an exact expected arrival time), CHASE (trace with the bank), AMEND, CANCEL & REISSUE, or ESCALATE to a human.
5. Pay-once lock: a reissue is only allowed after the original transfer is proven dead (cancelled, failed, or returned to the wallet). Every payout uses an idempotency key. Borrowed from distributed systems (sagas and fencing tokens), this makes a double payment impossible by design, not just unlikely.
6. Communicate: sends the supplier a clear update with proof (transfer ID, amount, status, expected arrival), and writes a post-incident report for the finance team.

FRAUD SHIELD
During an incident, any request to change bank details is treated as hostile until verified outside email (for example a call-back to a known phone number approved by a human). Emails and invoice PDFs are untrusted data. Following the CaMeL design (Google DeepMind, 2025), they can never change what the agent decides to do. A fake "new bank account" email cannot redirect money.

EXAMPLE FOR THE DEMO
EUR 48,500 sent to a German supplier on Tuesday. On Friday the supplier says it hasn't arrived and threatens to stop shipping. PayOnce finds the transfer was sent after the cut-off just before a bank holiday and is still in flight. It predicts arrival on Monday at 11:00, sends the supplier proof of payment, and blocks the urge to resend. An hour later a "please pay our new account" email arrives. PayOnce flags it as likely fraud and escalates. Monday: the money lands, the incident closes, and the total paid is still exactly EUR 48,500.

WHAT JUDGES WILL SEE
An incident war room with:
- a live evidence timeline built from Airwallex API calls
- ranked root causes with confidence levels
- the decision with its reasoning
- a "paid so far vs invoice" meter that can never go over 100%
- a fraud alert when the fake bank-change email arrives
- a button to inject failures (returned transfer, wrong name, compliance hold) and watch the agent resolve each one

AIRWALLEX APIs
Transfers (create, status, cancel where possible), Webhooks, Beneficiaries, Balances, plus Claude for reasoning and supplier messages.

BUILD PLAN (OCT 27 - NOV 13)
Week 1: Airwallex sandbox connection, transfer monitoring, incident timeline.
Week 2: diagnosis and decision engine, pay-once lock, fraud shield.
Week 3: war-room UI, failure scenarios, demo video.

IMPACT
Fewer double payments, fewer supplier shutdowns, a block against BEC fraud at its most dangerous moment, and a full audit trail for every incident.
