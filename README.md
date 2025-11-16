# GenDine

GenDine is an AI-driven front-of-house automation platform that uses **Stripe payment events as the real-time system boundary** for:

- Waitlist optimisation and demand forecasting  
- Table turnover analytics and pacing insights  
- Reconciliation-ready payout visibility for operators  

GenDine treats **Stripe’s event stream and timestamps as the source of truth** for all financial state changes. Local venue state is derived, synchronised, and audited against Stripe to ensure consistency.

---

## Core Concepts

### 1. Stripe-Native Event Stream
- Ingests `payment_intent.*`, `checkout.session.*`, and payout lifecycle events.
- Stripe timestamps provide authoritative ordering for financial events.
- All financial state transitions are driven by Stripe.

### 2. Venue State Store
- Stores venue seating, waitlist, table occupancy, and order lifecycle.
- Ties seating + waitlist events to Stripe payment confirmations.
- Maintains a consistent snapshot for analytics and operator dashboards.

### 3. Analytics Pillars
- **Seat Utilisation** — real-time occupancy vs. forecasting model.
- **Waitlist Accuracy** — predicted vs. actual seating times.
- **Revenue Pace** — projected vs. actual inflows from Stripe events.

---

## Reconciliation Overview

GenDine performs periodic payout reconciliation by aligning Stripe payout summaries with local ledgers.

Reconciliation workflow:

1. Consume latest payout events from Stripe.  
2. Fetch local payout expectations tied to the same payout ID.  
3. Compare total amounts + per-venue breakdowns.  
4. Flag mismatches for operator review (Stripe remains immutable).  
5. Append audit log entries for reconciliation adjustments.

Reconciliation is deterministic, audit-safe, and purely additive: Stripe is always the financial source of truth.

---

## Safety & Compliance

- No API keys, merchant names, or sensitive real data appear in any documentation.  
- Example datasets are fully synthetic and time-shifted.  
- Architecture/flow diagrams describe conceptual data movement, not internal code.  

---

## Documentation Contents

This repository includes:

- `README.md` (this file)  
- `docs/reconciliation-flow.txt`  
- `docs/waitlist-flow.txt`  
- `docs/pseudocode.md`  
- `example-data/fake-events.csv`  

Each file illustrates a different subsystem: reconciliation logic, waitlist dynamics, and synthetic examples of Stripe + venue events.
