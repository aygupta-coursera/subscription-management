# DESIGN.md — Passbook / Khata visual system

Direction locked (owner decision): **A. Bank passbook / khata ledger.**
Reference artifacts: Indian savings-account passbooks (pale green-grey paper,
guilloche band, 9-pin dot-matrix entries, rubber ink stamps), shopkeeper khata
registers (ruled columns, pencil margin notes, struck-through settled rows).

Anti-goals: warm-cream + serif + terracotta "retro" (the current AI-design default),
fake paper-grain overlays, skeuomorphic leather/binding, neo-brutalist borders
dressed as vintage. The aesthetic must carry information or it gets cut.

---

## 1. Color tokens

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#EDEFE6` | App background. Cool pale green-grey, NOT cream. |
| `--paper-band` | `#E3E8DB` | Alternating ledger-row band / guilloche zone. |
| `--rule` | `#B9C2B0` | Ledger rules (1px), column dividers, table borders. |
| `--ink` | `#1C2B45` | Primary text. Fountain-pen navy, never pure black. |
| `--ink-faint` | `#5A6B85` | Secondary text, captions, closed-period entries. |
| `--carbon` | `#2E4E9E` | Interactive elements, links, focus ring. Carbon-copy blue. |
| `--stamp-red` | `#A93226` | Debits (DR amounts), danger stamps (MANDATE LIVE, OVERDUE). |
| `--stamp-purple` | `#5B3E8C` | Pending/attention stamps (TRIAL ENDS, PRICE CHANGED). |
| `--stamp-green` | `#2F6B3C` | Settled/safe stamps (REVOKED ✓ complete, CREDIT). |

Rules:
- Red is reserved for money leaving and danger. Never decorative.
- No gradients anywhere. Flat ink on paper only.
- Dark mode: out of scope v1 (a passbook has no dark mode; don't fake one).

## 2. Typography

| Role | Face | Notes |
|---|---|---|
| Ledger data (amounts, dates, entry rows) | **Doto** (fallback: Workbench, then monospace) | Dot-matrix. Tabular figures. This is the voice of the machine that printed your passbook. |
| UI chrome, headers, labels | **IBM Plex Sans** | Bureaucratic, neutral, disciplined. Sentence case labels, UPPERCASE column heads with letter-spacing. |
| Stamps | IBM Plex Sans Condensed, 700, UPPERCASE | Inside a 2px border box, rotated −2° to +2° (deterministic per entity id, so it doesn't reshuffle on render). |
| Margin notes / owner annotations | **Kalam** | Handwritten, Indian-designed. Only for user-authored notes. |

Scale: 13px base for ledger rows (dense is correct — passbooks are dense),
11px captions, 20/28px page headers. Line-height tight (1.35) in tables,
normal (1.6) in prose.

## 3. Layout

- Page metaphor: the app is a register. Max-width ~880px, centered, with a
  left "binding margin" (thicker rule) where page-level metadata lives
  (page no., period, totals carried forward).
- Every list is a ruled table: full-width hairline rules between rows,
  column dividers only between the amount columns (PARTICULARS | RAIL | DR | DATE).
- Amounts right-aligned, monthly-equivalents in `--ink-faint` beneath actuals.
- "Totals carried forward" row at top of each ledger page — the running
  monthly burn, exactly like balance-brought-forward in a passbook.
- Wireframe (ledger view):

```
┌─┬──────────────────────────────────────────────────────┐
│ │  KHATA — SUBSCRIPTION REGISTER          PAGE 03      │
│ │  PERIOD: SEPTEMBER 2026        B/F: ₹7,842.00 /mo    │
│ ├──────────────────────────────────────────────────────┤
│ │  PARTICULARS          RAIL        DR        NEXT     │
│ │  ──────────────────────────────────────────────────  │
│ │  Claude Pro           UPI-M     ₹1,999    04 SEP     │
│ │    ~note: keep. daily driver~                        │
│ │  Perplexity Pro       CARD-EM   ₹1,650    07 SEP     │
│ │            [TRIAL ENDS 07 SEP]  ← stamp, purple      │
│ │  ~~Notion Plus~~      UPI-M     ₹  830    12 SEP     │
│ │            [MANDATE LIVE]       ← stamp, red         │
│ └──────────────────────────────────────────────────────┘
```

## 4. Signature element: the stamp system

Status is never a badge/pill. Status is a rubber stamp:

| State | Stamp | Ink |
|---|---|---|
| Trial converting soon | `TRIAL ENDS {DD MMM}` | purple |
| Price increased | `PRICE CHANGED {old}→{new}` | purple |
| Cancelled at merchant, mandate alive | `MANDATE LIVE` | red |
| Debit ≤7 days | `DUE {DD MMM}` | red |
| Fully dead (merchant + rail) | `REVOKED` + row strike-through | green |
| Paused | `PAUSED` | purple |

Stamp construction: bordered box, condensed caps, deterministic rotation,
`opacity: .85` with a subtle radial mask to fake uneven inking (CSS only,
no texture images). Stamps are clickable — they ARE the "needs attention"
affordance and deep-link to the resolving action.

## 5. Motion

One orchestrated moment: applying a state change stamps the row — scale
1.15→1.0 with a 90ms ease-out and a 1px settle, like a hand stamp landing.
Row strike-through draws left→right over 200ms on revoke.
Everything else is instant. `prefers-reduced-motion`: all of the above off.

## 6. Voice & copy

- Register terseness: "DR ₹1,999 · 04 SEP", "B/F", "C/F". Full sentences only
  in explanatory empty states.
- Actions say what they do: "Mark mandate revoked", "Log price change",
  "Stamp as paused". The button that says "Revoke logged" toasts "Revoked."
- Errors are direct and unapologetic: "Amount missing. Every entry needs a DR value."
- Empty ledger: "No entries this period. Add your first subscription — the
  register only works if it's complete."

## 7. Quality floor

Responsive to 380px (binding margin collapses, RAIL column folds into the
particulars line). Visible focus ring in `--carbon`. All money in integer
minor units. Keyboard: `/` focuses quick-add, `Enter` commits, `S` stamps
selected row. WCAG AA contrast verified for all ink-on-paper pairs.
