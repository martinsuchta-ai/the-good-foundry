# Sales Commission Policy — Subscriptions & Expansion

**The Good Foundry Pty Ltd** · Internal · Effective [DATE] · Owner: [SALES OPS]

---

## 1. Purpose

This policy sets out how sales commission is earned and split between **Sales Representatives** and the **Service / Delivery team** across the full lifecycle of a Wellbeing Matters account — from the initial subscription through to post-sale add-ons and expansions (further editions, 360 Foundry, Rewards Foundry Power-Up, bespoke extensions, and other up-sells).

It exists to reward the right people for the right work: the rep who lands the account, and the service team who grow it.

---

## 2. The principle

Commission tracks **effort and influence**. The rep who opens an account earns the full rate on what they personally close, and a smaller originator credit on what others grow afterwards. When the rep and the service team genuinely sell an expansion together, they share equally.

**Ownership of any commission event is determined by whose name is on the accepted proposal (offer).** Whoever raises a proposal that the customer accepts owns that event. This is the single, objective attribution rule — no time-boxing, no after-the-fact arguments.

---

## 3. The commission structure

| # | Commission event | Owner (= accepted proposal) | Rep | Service team |
|---|---|---|---|---|
| 1 | **Initial subscription** — rep closes the new-business sale | Rep | **30%** | — |
| 2 | **Service-led add-on** — service team raises & closes; rep not involved | Service | **5%** (originator credit) | **15%** |
| 3 | **Joint up-sell** — service-led proposal, rep materially assists | Service | **15%** | **15%** |
| 4 | **Rep-led up-sell** — rep raises & closes the expansion themselves | Rep | **30%** | — |

All percentages apply to the **top-line** value of the relevant subscription or add-on.

### Worked example (illustrative)

| Event | Revenue | Rep | Service team |
|---|---|---|---|
| Initial subscription | $100,000 | $30,000 | — |
| Service-led add-on (rep not involved) | $20,000 | $1,000 | $3,000 |
| Joint up-sell (rep assists) | $30,000 | $4,500 | $4,500 |
| **Totals** | **$150,000** | **$35,500** | **$7,500** |

---

## 4. Definitions & rules

**Accepted proposal = ownership.** The name on the proposal the customer accepts owns the commission event. If the rep's name is on it, event type 1 or 4 applies (30% rep). If the service team's name is on it, event type 2 or 3 applies.

**"Rep materially assists" (the line between event 2 and event 3).** A rep counts as assisting — unlocking the 15%/15% split instead of 5%/15% — **only if they are named on the proposal / opportunity in the CRM before the customer accepts it.** Named on the deal = assist. Not named = service-led. This is recorded, not recalled, so it cannot be re-litigated after payout.

**Originator credit boundary.** The rep's 5% originator credit (event 2) applies for as long as the rep remains the **named account owner**. Once an account formally transitions to an account manager or service owner, the 5% originator credit stops — or transfers to the new owner — from the date of transition. This prevents a rep clipping 5% indefinitely on accounts they no longer touch.

**Cost note for approvers.** A joint up-sell (event 3) costs 30% in total commission — the same as new business. That is intentional where two people genuinely contributed, but it makes event 3 the most expensive expansion type. The "named before acceptance" test is what keeps it controlled.

---

## 5. Edge cases

- **Rep raises the proposal but service negotiates the detail:** ownership still follows the accepted proposal. If the rep's name is on it, it's event 4 (30% rep).
- **Multiple add-ons in one accepted proposal:** classify the whole proposal by its owner and whether the rep is named; apply one event type to the bundle.
- **Account in transition when an add-on closes:** the named account owner on the date of acceptance governs the originator credit.

---

## 6. Governance

Disputes are resolved by [SALES OPS / FINANCE] against the CRM record of the accepted proposal and the named-owner field. Because attribution is tied to recorded CRM state at the moment of acceptance, the record is the single source of truth. This policy is reviewed [annually / each plan year].

---
---

# Implementation brief — for Claude Code

**Goal:** Build a back-end page that distributes this policy to reps and lets them model their own commission. Match The Good Foundry website theme (https://www.the-good-foundry.com).

## Brand / theme

- **Voice:** research-led, plain-spoken, confident, warm. Short declarative sentences. "Evidence over opinion." Avoid hype.
- **Logo:** The Good Foundry gradient primary mark (`/logos/TGF_logo/good-foundry-gradient-primary_2560.png`). Pull the live asset; do not recreate.
- **Visual language from the site:** soft gradient primary brand colour, generous whitespace, numbered section markers (01 / 02 / 03 style), large readable headings, no traffic-light colours, clean tables. Mirror the site's section-card rhythm.
- **Typography:** match the live site's font stack (inspect the deployed CSS); fall back to a clean humanist sans-serif.
- **Footer:** "The Good Foundry Pty Ltd · ACN 103 558 372 · 1/31 Beach Street, VIC 3199, Australia" · link privacy policy · © 2026.

## Page requirements

1. **Policy display** — render sections 1–6 above as a clean, branded read-only page. Numbered section markers in the site style.
2. **Commission calculator** — interactive panel where a rep selects an **event type** (1–4) and enters a **deal value**, and the page returns the rep payout and service-team payout. Encode the rate table:
   - Event 1 (Initial subscription): rep 30%, service 0%
   - Event 2 (Service-led add-on): rep 5%, service 15%
   - Event 3 (Joint up-sell): rep 15%, service 15%
   - Event 4 (Rep-led up-sell): rep 30%, service 0%
3. **"Which event is this?" helper** — a short decision flow:
   - *Who is named on the accepted proposal?* Rep → did the rep close new business (event 1) or an expansion (event 4)? Service → *is the rep also named on the proposal before acceptance?* Yes → event 3. No → event 2.
4. **Worked-example table** — render the section 3 example as a styled table.
5. **Access** — back-end / authenticated; reps only. No public route.

## Technical notes

- Rates must live in a **single config object** (e.g. `COMMISSION_RATES`) so Sales Ops can change them in one place without touching layout.
- Keep all calculation client-side and stateless; no storage of rep figures required for v1.
- All percentages apply to top-line deal value (no discounting logic in v1 — flag as a future enhancement).
- Make the originator-credit boundary and the "named before acceptance" rule visible as inline help text next to the relevant calculator options.

## Out of scope for v1

Discount handling, multi-currency, CRM integration / auto-classification from the proposal record, payout scheduling. Note these as future enhancements.
