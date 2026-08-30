# DESIGN.md — Passbook / Khata visual system

Direction locked (owner decision): **A. Bank passbook / khata ledger.**
Amended v1.1 (owner review of a real bahi khata artifact): the primary reference
shifts from bank passbook to **bahi khata register** — warm paper, madder-red
ruled columns, double-page spread, ditto marks, double-ruled totals. Approved
via rendered mockup. Dot-matrix data voice and the stamp system carry over
unchanged.
Amended v1.2 (owner review of a 1920 school report card artifact): form
language (dotted leader-line fields), printed rubric footer, period-close
verdict ritual, and the provenance rule — machine facts print, human
judgments write. Serif-label A/B resolved: no serif; IBM Plex holds.
Reference artifacts: shopkeeper bahi khata registers (red-ruled columns,
handwritten margin notes, ditto marks, boxed totals, struck-through settled
rows) + Indian bank passbooks (9-pin dot-matrix entries, rubber ink stamps).

Anti-goals: the cream + display-serif + terracotta "retro" combo (the current
AI-design default — note: `--paper` is warm, but identity comes from the red
rules, dot-matrix, and stamps, never from the background plus a pretty serif),
fake paper-grain overlays, photorealism (wood, stains, page curl, 3D binding),
neo-brutalist borders dressed as vintage. The aesthetic must carry information
or it gets cut.

---

## 1. Color tokens

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#F1EEDF` | App background. Warm aged bahi paper — muted, never orange-cream. |
| `--paper-band` | `#EAE6D3` | Page edge zones, gutter shading, closed-period pages. |
| `--rule-red` | `#C4756B` | Primary ledger rules: column heads, section dividers. Faded madder. |
| `--rule-red-faint` | `#E0B7B0` | Vertical column dividers between amount columns. |
| `--rule-soft` | `#E4DECA` | Hairline rules between entry rows. |
| `--ink` | `#22304C` | Primary text. Fountain-pen navy, never pure black. |
| `--ink-faint` | `#5A6B85` | Secondary text, captions, struck/dead entries. |
| `--ink-label` | `#8A5A54` | Column-head labels, small caps eyebrows. Dried red-brown ink. |
| `--carbon` | `#2E4E9E` | Interactive elements, links, focus ring. Carbon-copy blue. |
| `--stamp-red` | `#A93226` | Debits (DR amounts), danger stamps (MANDATE LIVE, OVERDUE, DUE). |
| `--stamp-purple` | `#5B3E8C` | Pending/attention stamps (TRIAL ENDS, PRICE CHANGED, PAUSED). |
| `--stamp-green` | `#2F6B3C` | Settled/safe stamps (REVOKED ✓ complete, CREDIT). |

Rules:
- **Two reds, two jobs.** `--rule-red` (desaturated madder) is *structural* — it
  is the grid of the bahi and carries no alarm. `--stamp-red` (saturated) is
  *semantic* — money leaving and danger only. They must stay visually distinct;
  never use one for the other's job.
- No gradients anywhere. Flat ink on paper only.
- No photorealism: no wood, stains, ink blots, page curl, paper grain, or
  texture images. The book's grammar, never its photograph.
- Dark mode: out of scope v1 (a bahi has no dark mode; don't fake one).

## 2. Typography

| Role | Face | Notes |
|---|---|---|
| Ledger data (amounts, dates, entry rows) | **Doto** (fallback: Workbench, then monospace) | Dot-matrix. Tabular figures. This is the voice of the machine that printed your passbook. |
| UI chrome, headers, labels | **IBM Plex Sans** | Bureaucratic, neutral, disciplined. Sentence case labels, UPPERCASE column heads with letter-spacing. |
| Stamps | IBM Plex Sans Condensed, 700, UPPERCASE | Inside a 2px border box, rotated −2° to +2° (deterministic per entity id, so it doesn't reshuffle on render). |
| Margin notes / owner annotations | **Kalam** | Handwritten, Indian-designed. Only for user-authored notes. |

Form labels use IBM Plex Sans (serif A/B resolved 2026-08: rejected — a
fourth voice with no role; see §5 provenance rule for the three-voice map).

Scale: 13px base for ledger rows (dense is correct — passbooks are dense),
11px captions, 20/28px page headers. Line-height tight (1.35) in tables,
normal (1.6) in prose.

## 3. Layout — the open spread

- Page metaphor: the app is an **open bahi** — a two-page spread with a center
  gutter. Max-width ~1100px centered; each page ~520px.
- **Left page = the record.** The register itself: entries, rules, running totals.
- **Right page = "it comes to me."** Needs-attention queue (live stamps) on top,
  upcoming 30-day debits + cash-out total beneath. Levels 0–1 of the information
  hierarchy, always visible, no tabs.
- The gutter is a 1px `--paper-band` fold line — a divider, never a rendered
  3D binding.
- **Responsive: the book closes.** Below ~900px the spread collapses to a single
  page; register ↔ attention becomes a page-turn navigation (instant swap, or a
  ≤150ms horizontal slide; off under `prefers-reduced-motion`). Never a hamburger.
- Every list is a ruled table: `--rule-red` under column heads, `--rule-soft`
  hairlines between rows, `--rule-red-faint` vertical dividers only between the
  amount columns (PARTICULARS | RAIL | DR | NEXT).
- Amounts right-aligned, DR values in `--stamp-red`, monthly-equivalents in
  `--ink-faint` beneath actuals.

### Totals conventions (accounting-authentic)
- **B/F (brought forward)** — running monthly-equivalent burn — lives in the
  left page header, under the title, above the first `--rule-red`.
- **C/F (carried forward)** — the closing total — sits at the **foot of the
  column, right-aligned, boxed by a single rule above and a double rule below.**
  The double rule means "final." Nothing renders below it on the page.
- The right page's parallel total is **CASH OUT ₹X** (next-30-day sum), same
  single-rule-above treatment.

### Ditto marks (bundle rendering)
- A bundle parent gets a full particulars line (DR "—" if the parent itself
  doesn't bill).
- Bundle children render indented beneath with a **ditto mark (") replacing the
  repeated context**, each keeping its own DR, cadence, and NEXT clock — the
  khata convention for "same as above," and exactly the Lenny's-bundle model:
  one signup, N independent clocks, visible at a glance.
- Striking through a parent does not strike children; each child dies on its
  own clock.

- Wireframe (open spread, desktop):

```
┌───────────────────────────────┬───────────────────────────────┐
│ KHATA · SUBSCRIPTION REGISTER │ NEEDS ATTENTION · 3           │
│ B/F ₹7,842 /mo · SEP 2026     │ ───────────────────────────── │
│ ───────────────────────────── │ [MANDATE LIVE]      ← red     │
│ PARTICULARS │ RAIL │ DR │NEXT │  Notion Plus — revoke in GPay │
│ Claude Pro    UPI-M 1,999 04S │ [TRIAL ENDS 07 SEP] ← purple  │
│  ~keep. daily driver~         │  Perplexity → ₹1,650/mo       │
│ Lenny's Bundle        —       │ [DUE 04 SEP]        ← red     │
│  " Perplexity  CARD 1,650 07S │  Claude Pro ₹1,999 UPI-M      │
│  " Granola     CARD   830 19S │ ───────────────────────────── │
│ ~~Notion Plus~~ UPI-M 830 12S │ NEXT 30 DAYS                  │
│                               │  04 SEP · ₹1,999 · Claude Pro │
│           ┌─────────────────┐ │  07 SEP · ₹1,650 · Perplexity │
│           │ C/F ₹9,142 /mo  │ │  ─────────────                │
│           └═════════════════┘ │  CASH OUT ₹4,479              │
└───────────────────────────────┴───────────────────────────────┘
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

## 5. Form language & provenance (v1.2)

### Provenance rule — the governing principle
**Machine-observed facts print; human judgments write.** Three voices, three
roles, never mixed:
- Dot-matrix (Doto) = what the system observed or computed: amounts, dates,
  rails, totals.
- IBM Plex = the printed form itself: labels, column heads, rubric, chrome.
- Kalam = what the owner decided or annotated: margin notes, verdicts,
  pencil-entry confirmations. Kalam NEVER renders system data.
This extends to entry states: proposed (pencil — `--ink-faint`, dotted
underline) vs confirmed (inked — full `--ink`). Confirming an entry is an
owner judgment, so the confirm action is voiced in Kalam.

### Dotted leader-line fields (`DottedField`)
All form inputs follow the printed-form convention: printed label (Plex,
`--ink-label`), dotted leader line (`--rule-soft` dots), value written on the
line. No boxed inputs anywhere. Focus state: leader dots turn `--carbon`;
caret sits ON the line. Errors print beneath the line in `--stamp-red`,
register-terse (§7 voice).

### Rubric footer (`Rubric`)
Each register page may carry ONE printed legend line at the foot, below
nothing else but the page edge, explaining the stamp inks:
"RED — money leaving or action overdue · PURPLE — a clock is running ·
GREEN — settled." 11px Plex, `--ink-faint`. Hard limit: one line. If it
needs two, simplify the thing it explains instead.

### Verdict ritual (period close)
At month end, every entry flagged during the period gets a printed question
in the review page: "Keep for {MMM}? — " with a dotted leader. The owner's
answer renders in Kalam and is logged as a judgment (with date) in the
entry's history. Default-renewal is the enemy; the ritual converts renewal
into a recorded decision.

## 6. Motion

One orchestrated moment: applying a state change stamps the row — scale
1.15→1.0 with a 90ms ease-out and a 1px settle, like a hand stamp landing.
Row strike-through draws left→right over 200ms on revoke.
Everything else is instant. `prefers-reduced-motion`: all of the above off.

## 7. Voice & copy

- Register terseness: "DR ₹1,999 · 04 SEP", "B/F", "C/F". Full sentences only
  in explanatory empty states.
- Actions say what they do: "Mark mandate revoked", "Log price change",
  "Stamp as paused". The button that says "Revoke logged" toasts "Revoked."
- Errors are direct and unapologetic: "Amount missing. Every entry needs a DR value."
- Empty ledger: "No entries this period. Add your first subscription — the
  register only works if it's complete."

## 8. Quality floor

Responsive to 380px: the book closes to a single page below ~900px (see §3);
below ~480px the RAIL column folds into the particulars line. Visible focus
ring in `--carbon`. All money in integer minor units. Keyboard: `/` focuses quick-add, `Enter` commits, `S` stamps
selected row. WCAG AA contrast verified for all ink-on-paper pairs.
