# CLAUDE.md — Subscription Ledger (working name: "Khata")

> Personal web app that tracks every recurring charge I have — across UPI AutoPay,
> cards, app stores, and email invoices — and comes to me *before* money leaves,
> instead of me going to it after.

---

## 1. The problem

Recurring charges are designed to be invisible. The cognitive load of tracking them
has been fully transferred to the user, and users are failing at it:

- Consumers estimate spending **$86/month** on subscriptions; actual itemized average
  is **$219/month** — a 2.5x perception gap (C+R Research, 2022 benchmark, still the
  industry-cited figure in 2026).
- **89%** of consumers underestimate their subscription spend; **66%** by more than
  $200 (West Monroe).
- **72%** have everything on auto-pay; **74%** say recurring charges are easy to
  forget; **42%** have forgotten a subscription entirely while still being charged.
- Americans waste **$15.5B+/year** on forgotten subscriptions.

### The AI layer (why this got urgent in 2025–26)
- Average AI subscriber pays **$65.90/month across 4 AI tools**; **24%** spend
  $100+/month (Bango, "The Rise of the AI Subscriber," Nov 2025).
- **53%** of AI subscribers cancel-and-restart tools as needed → high mandate churn,
  many dormant-but-live mandates.
- **75%** want AI subscriptions consolidated into one bill; **61%** cut other
  subscriptions to afford AI; **54%** call AI pricing a "rip-off."
- Standard tier converged at **$20/month** (ChatGPT Plus, Claude Pro, Google AI Pro,
  Perplexity Pro); power tiers run **$100–$300**. All five majors ≈ **$110/month**.
- Bundle deals (e.g. Lenny's Newsletter bundle) spawn many independent trial clocks
  from one signup event — each converting to paid separately, on different dates,
  on different instruments. **This is the personal trigger for this project.**

### The India / UPI AutoPay aggravation (primary user context)
- Mandates live at the **bank/NPCI layer**, not the app. Deleting the app, or even
  cancelling on the merchant site, does NOT revoke the mandate. Cancellation and
  revocation are two separate acts users don't know they must both perform.
- Mandates are scattered across GPay / PhonePe / Paytm / bank apps / Play Store.
  No single user-facing unified view existed until NPCI's UPI Help portal, which
  covers only the UPI rail.
- RBI requires a **pre-debit notification ≥24h** before every recurring UPI debit
  with amount + cancel option (also for card e-mandates; OTP/AFA above ₹15,000).
  In practice these notifications are missed, unclear, or lost in SMS noise.
- Documented user losses of **₹500–₹5,000/year** to forgotten mandates; audits
  routinely surface ₹1,000+/month in dead subscriptions.

### Problem statement (canonical)
Power users of digital and AI tools accumulate 10–20+ recurring charges across
multiple payment rails (UPI AutoPay, credit/debit card e-mandates, app stores,
annual invoices) with no unified view. Because discovery, tracking, and cancellation
each live in different systems, users systematically underestimate spend by ~2.5x,
pay for forgotten or duplicate tools, and experience bill shock. Existing tools force
a bad trade-off between automation (full bank access) and privacy/coverage — and
none of them work properly on Indian rails.

---

## 2. Voice of customer (secondary research synthesis)

Six recurring themes, in rough order of frequency:

1. **"I didn't know I was still paying."** Forgotten subs, silent trial conversions.
2. **"Cancelling is deliberately hard."** Sign-up: 30 seconds. Cancel: 20-minute
   ordeal. In India, doubled by the merchant-cancel vs mandate-revoke split.
3. **"Annual billing and trials ambush me."** Annual charges feel like fraud when
   they land; ₹/$ small monthly charges are invisible individually, devastating
   collectively.
4. **"I don't trust trackers with my bank login."** Bank-scanning apps route full
   transaction history (salary, rent, medical) through aggregators. Dealbreaker for
   a large segment.
5. **"Even the category leader breaks."** Rocket Money: 4.5★ app stores vs 3.5★
   Trustpilot. Month-one discovery delights; long-term reliability (sync errors,
   lockouts, ~21.5% support resolution rate) disappoints.
6. **"My AI tools overlap and I can't tell what to keep."** (Blind/dev-forum theme.)
   No tool shows usage-vs-cost. Power users track token burn in their heads.
   $70–$110/month stacks with heavy capability overlap are the norm.

---

## 3. Competitive landscape and gaps

| Archetype | Examples | Detection | Fatal flaw for me |
|---|---|---|---|
| Bank-linked finance apps | Rocket Money, PocketGuard, Emma, Monarch | Bank/card txn scan | No India/UPI rails; privacy tax; reliability decay |
| Manual trackers | Bobby, TrackMySubs, Wallos, Eyespender | User types it in | Can't discover what I forgot; goes stale |
| Email/receipt parsers | SubDupes et al. | Parse invoice emails | Misses no-receipt charges; single signal |
| Rail natives | NPCI UPI Help portal, bank apps, CRED Money | Rail-level visibility | Each rail sees only itself; no intelligence layer |

### Gaps this product exploits
1. **Cross-rail unification for India** — UPI mandates + card e-mandates + app store
   + email invoices in one ledger. Nobody does this. Worst-served persona = Indian
   power user. That's me.
2. **Automation without bank login** — combine multiple low-trust signals:
   email receipts (Gmail) + SMS debit/pre-debit alerts (ubiquitous in India) +
   manual/CSV + periodic NPCI mandate audit prompts.
3. **Usage-vs-cost intelligence** — especially for AI tools: "you haven't opened X
   in 40 days; Y covers 80% of what you use it for."
4. **Trial & bundle lifecycle** — model one signup event → N renewal clocks.
   First-class support for bundle blasts (Lenny's-style).
5. **Close the cancellation loop** — "you cancelled the service; your mandate is
   still live — revoke it here" with deep links per UPI app / bank.
6. **Trust durability** — boring, reliable, local-first. Win where the leader decays.

---

## 4. Product principles

1. **It comes to me.** Proactive by default: pre-debit digests, trial-conversion
   warnings, price-hike flags, orphan-mandate alerts. The dashboard is secondary;
   the notification is the product.
2. **No bank credentials, ever.** Signals only: email parse, SMS parse (future),
   manual entry, CSV import. Local-first storage.
3. **The ledger is the source of truth.** Every subscription is an entity with a
   lifecycle: trial → active → price-changed → cancelled-at-merchant →
   mandate-revoked → dead. State transitions are explicit and audited.
4. **India-first, not India-only.** UPI mandate semantics (mandate ≠ subscription)
   are modeled natively; multi-currency from day one (₹ primary, $ secondary).
5. **Honest numbers.** Show true monthly-equivalent cost (annuals amortized),
   projected next-30-day cash out, and per-category burn. Kill the perception gap.
6. **Personal tool first.** Built for one opinionated power user. No auth flows,
   billing, or multi-tenant complexity in v1.

---

## 5. MVP scope (v1)

**In:**
- Subscription entity model: name, vendor, rail (UPI mandate / card e-mandate /
  app store / direct), instrument, amount, currency, cadence, next-debit date,
  trial-end date, source-of-truth links (cancel URL, mandate location), status,
  category (AI / streaming / SaaS / news / utility / other), notes.
- Bundle model: parent bundle → child subscriptions with independent clocks.
- Views: (a) upcoming 30-day debit calendar, (b) monthly-equivalent burn by
  category, (c) full ledger, (d) "needs attention" queue (trials ending,
  price changes, orphan mandates).
- Alerts: in-app + email digest (daily or weekly) for anything landing in ≤7 days.
- Manual entry + CSV import. Fast add (single input line, parsed).
- Local persistence (SQLite or file-backed) — data never leaves my machine/server.

**Out (v1):** Gmail auto-parse (v2), SMS parse (v2/mobile), cancellation concierge,
usage tracking integrations, multi-user, mobile app.

---

## 6. Tech notes (proposed, to confirm)

- Web app, single user, self-hosted or local.
- Stack candidate: React + Vite frontend, light Node/Express or purely local
  (IndexedDB/SQLite via API) backend. Keep it boring.
- All money math in integer minor units (paise/cents). Amortization: annual/12,
  quarterly/3, etc., displayed as monthly-equivalent.
- Timezone: Asia/Kolkata for all debit-date logic.

---

## 7. Visual direction — DECIDED IN DISCUSSION, DO NOT DEFAULT

Constraint from owner: "vintage nostalgia, but not clichéd." Explicitly banned:
generic 70s-cream-and-orange retro, Instagram-filter grain, fake paper texture
slapped on a normal SaaS layout, neo-brutalism dressed as retro.

Candidate directions under discussion (pick ONE and commit fully):
- **A. Bank passbook / khata ledger** — Indian bank passbook & shopkeeper khata:
  ruled ledger lines, dot-matrix numerals, ink stamps for state changes
  ("REVOKED", "TRIAL ENDS"), carbon-copy blues, hand-numbered pages. Domain-native
  nostalgia: this is literally what money records used to look like here.
- **B. Railway reservation chart / ticket** — Indian Railways ephemera: cardboard
  Edmondson tickets, dot-matrix reservation charts, stencil type, punch holes as
  status markers.
- **C. Teletext / CRT terminal** — phosphor mono/amber, scanline restraint,
  tabular density; nostalgia of early banking terminals.
- **D. Vintage Indian print ephemera** — matchbox-label color plates, hand-painted
  signage type; most decorative, hardest to keep functional.

Whichever is chosen: the aesthetic must carry *information* (stamps = status,
ledger rules = alignment, ticket punches = lifecycle), never decoration alone.
Typography, color tokens, and interaction metaphors get defined in a separate
DESIGN.md after the choice.

---

## 8. Key numbers to reuse (with source + vintage)

| Stat | Value | Source, year |
|---|---|---|
| Perceived vs actual monthly spend | $86 vs $219 | C+R Research, 2022 (industry benchmark) |
| Underestimate spend | 89% (66% by >$200) | West Monroe |
| All subs on autopay | 72% | West Monroe |
| Forgot an active sub | 42% | C+R / West Monroe |
| Annual waste, forgotten subs (US) | $15.5B+ | SubStop research roundup, 2026 |
| Avg AI tools per subscriber / spend | 4 tools / $65.90/mo | Bango, Nov 2025 |
| Want one consolidated AI bill | 75% | Bango, Nov 2025 |
| Cancel-and-restart AI tools | 53% | Bango, Nov 2025 |
| All five major AI standard tiers | ≈$110/mo, $1,320/yr | Multiple pricing analyses, 2026 |
| Forgotten UPI mandate leakage | ₹500–₹5,000/yr per user | NPCI UPI Help portal coverage, 2026 |
| RBI pre-debit notification rule | ≥24h before debit; AFA >₹15,000 | RBI e-mandate directives |

Caveat: the $86/$219 gap traces to a 2022 survey recycled industry-wide.
Directionally solid; re-validate before any external-facing use.

---

## 9. Open questions

1. Visual direction: A/B/C/D above — decide before any UI code.
2. Name: "Khata" placeholder. Alternatives welcome.
3. v1 alert channel: email digest vs browser notifications vs both.
4. Where does it run: fully local (laptop) vs small always-on server (needed for
   scheduled alerts to actually "come to me").
5. Gmail parse in v2: OAuth read-only scope acceptable, or forward-to-inbox
   parsing instead (privacy-cleaner)?

## 10. Working agreements for Claude on this project

- Step-by-step: framing → design direction → data model → UI skeleton → alerts.
  Don't skip ahead or scaffold the whole app unprompted.
- Never invent statistics; anything numeric ties back to §8 or new cited research.
- Aesthetic decisions must pass the "not clichéd" bar in §7; when in doubt, ask.
- This is a personal tool: bias to simplicity, no premature productization.
