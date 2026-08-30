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
Amended v1.3 (usability pass): added §8 interaction layer — control hierarchy,
required states, navigation, pane relationship, touch targets. Corrections:
red is scarce (amounts print in ink), stamps are either actionable or settled
(never ambiguously both), and Kalam is an output style, never an input method.
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
| `--paper-band` | `#EAE6D3` | Page edge zones, gutter shading, row hover/selected fill. |
| `--rule-red` | `#C4756B` | Primary ledger rules: column heads, section dividers. Faded madder. |
| `--rule-red-faint` | `#E0B7B0` | Vertical column dividers between amount columns. |
| `--rule-soft` | `#E4DECA` | Hairline rules between entry rows. |
| `--ink` | `#22304C` | Primary text AND all routine amounts. Fountain-pen navy, never pure black. |
| `--ink-faint` | `#5A6B85` | Secondary text, captions, struck/dead entries, proposed (pencil) entries. |
| `--ink-label` | `#8A5A54` | Column-head labels, small caps eyebrows. Dried red-brown ink. |
| `--carbon` | `#2E4E9E` | Interactive elements only, links, focus ring. Carbon-copy blue. |
| `--stamp-red` | `#A93226` | Danger stamps (MANDATE LIVE, OVERDUE, DUE), ≤7-day amounts, errors. NOT routine debits. |
| `--stamp-purple` | `#5B3E8C` | Pending/attention stamps (TRIAL ENDS, PRICE CHANGED, PAUSED). |
| `--stamp-green` | `#2F6B3C` | Settled/safe stamps (REVOKED ✓ complete, CREDIT). |

Rules:
- **Two reds, two jobs.** `--rule-red` (desaturated madder) is *structural* — it
  is the grid of the bahi and carries no alarm. `--stamp-red` (saturated) is
  *semantic* — danger only. They must stay visually distinct; never use one for
  the other's job.
- **Red is scarce (corrected v1.3).** Routine DR amounts render in `--ink`, NOT
  red. `--stamp-red` is reserved for: danger stamps (MANDATE LIVE, OVERDUE),
  items due in ≤7 days, and error text. Colouring every debit red made a routine
  ₹830 shout as loudly as a live orphan mandate — the alarm stopped meaning
  anything.
- **Carbon is reserved (v1.3).** `--carbon` marks things that respond to
  interaction, and nothing else. Never use it for emphasis.
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
| Margin notes / owner annotations | **Kalam** | Handwritten, Indian-designed. Renders owner judgments only — never an input method (see §5). |

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
  hierarchy, always visible, no tabs. It is a derived lens on the left page —
  see §8.4.
- The gutter is a 1px `--paper-band` fold line — a divider, never a rendered
  3D binding.
- **Responsive: the book closes.** Below ~900px the spread collapses to a single
  page; register ↔ attention becomes a page-turn (instant swap, or a ≤150ms
  horizontal slide; off under `prefers-reduced-motion`). Page-turn covers ONLY
  that pair — all other destinations are pushes (§8.6). Never a hamburger.
- Every list is a ruled table: `--rule-red` under column heads, `--rule-soft`
  hairlines between rows, `--rule-red-faint` vertical dividers only between the
  amount columns (PARTICULARS | RAIL | DR | NEXT).
- Amounts right-aligned in `--ink`; only amounts due in ≤7 days or overdue take
  `--stamp-red`. Monthly-equivalents in `--ink-faint` beneath actuals.

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
│ KHATA · ‹ SEP 2026 › · Burn › · Review › · ? · [+ ADD ENTRY]   │  ← persistent header
├───────────────────────────────┼───────────────────────────────┤
│ SUBSCRIPTION REGISTER         │ NEEDS ATTENTION · 3           │
│ B/F ₹7,842 /mo                │ ───────────────────────────── │
│ ───────────────────────────── │ [MANDATE LIVE ›]    ← actionable│
│ PARTICULARS │ RAIL │ DR │NEXT │  Notion Plus — revoke in GPay │
│ Claude Pro    UPI-M 1,999 04S │ [TRIAL ENDS 07 SEP ›]         │
│  keep. daily driver           │  Perplexity → ₹1,650/mo       │
│ Lenny's Bundle        —       │ [DUE 04 SEP ›]                │
│  " Perplexity  CARD 1,650 07S │  Claude Pro ₹1,999 UPI-M      │
│  " Granola     CARD   830 19S │ ───────────────────────────── │
│ ~~Notion Plus~~ UPI-M 830 12S │ NEXT 30 DAYS                  │
│   [REVOKED]  ← settled, inert │  04 SEP · ₹1,999 · Claude Pro │
│           ┌─────────────────┐ │  07 SEP · ₹1,650 · Perplexity │
│           │ C/F ₹9,142 /mo  │ │  ─────────────                │
│           └═════════════════┘ │  CASH OUT ₹4,479              │
│ RED — leaving · PURPLE — clock · GREEN — settled              │
└───────────────────────────────┴───────────────────────────────┘
```

## 4. Signature element: the stamp system

Status is never a badge/pill. Status is a rubber stamp:

| State | Stamp | Ink | Kind |
|---|---|---|---|
| Trial converting soon | `TRIAL ENDS {DD MMM}` | purple | actionable |
| Price increased | `PRICE CHANGED {old}→{new}` | purple | actionable |
| Cancelled at merchant, mandate alive | `MANDATE LIVE` | red | actionable |
| Debit ≤7 days | `DUE {DD MMM}` | red | actionable |
| Paused | `PAUSED` | purple | actionable |
| Fully dead (merchant + rail) | `REVOKED` + row strike-through | green | settled |

Stamp construction: bordered box, condensed caps, deterministic rotation,
`opacity: .85` with a subtle radial mask to fake uneven inking (CSS only,
no texture images).

**Actionable vs settled stamps (corrected v1.3).** A stamp is either a control
or a label, never ambiguously both:
- **Actionable** — there is something to resolve. Solid 2px border,
  `cursor: pointer`, hover raises opacity .85 → 1 and shifts 1px up, focus ring
  in `--carbon`, trailing `›`, 44px minimum hit area. Clicking opens the resolve
  view for that entry.
- **Settled** — status only. `opacity: .6`, dashed 1px border, no pointer, not
  focusable, no `›`. It is a record, not an invitation.

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
underline) vs confirmed (inked — full `--ink`).

**Kalam is an output style, never an input method (corrected v1.3).** The owner
never handwrites into the interface. Judgments are captured with ordinary
controls — buttons, taps, typed text — and *rendered* in Kalam afterwards to
show provenance. "Keep / Cancel" are buttons; the recorded answer then appears
as handwriting. Typed margin notes use a normal text control and display in
Kalam once committed.

### Dotted leader-line fields (`DottedField`)
All form inputs follow the printed-form convention: printed label (Plex,
`--ink-label`), dotted leader line (`--rule-soft` dots), value written on the
line. No boxed inputs anywhere. Focus state: leader dots turn `--carbon`;
caret sits ON the line. Errors print beneath the line in `--stamp-red`,
register-terse (§7 voice). The hit target is the full row, not the line
(§8.5).

### Rubric footer (`Rubric`)
Each register page may carry ONE printed legend line at the foot, below
nothing else but the page edge, explaining the stamp inks:
"RED — money leaving or action overdue · PURPLE — a clock is running ·
GREEN — settled." 11px Plex, `--ink-faint`. Hard limit: one line. If it
needs two, simplify the thing it explains instead.

### Verdict ritual (period close)
At month end, every entry flagged during the period gets a printed question
in the review page: "Keep for {MMM}? — " with a dotted leader. The owner
answers by pressing **Keep** or **Cancel** (buttons, per §8.1); the recorded
answer then renders in Kalam on the leader line and is logged as a dated
judgment in the entry's history. Default-renewal is the enemy; the ritual
converts renewal into a recorded decision.

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

## 8. Interaction layer (v1.3)

The aesthetic was specified before the behaviour, which made the interface
unpredictable: controls were indistinguishable from labels, navigation was
absent, and states were undefined. This section is binding — no UI ships
without the behaviour below.

### 8.1 Control hierarchy — three tiers, three treatments
Every view declares exactly ONE primary action. Anything that is not in this
table is not a control and must not look like one.

| Tier | Treatment | Example |
|---|---|---|
| **Primary** (one per view) | Solid `--ink` 1px border, `--paper-band` fill, Plex 12 caps, 40px tall, full width on mobile | `INK ENTRY`, `STAMP AS REVOKED`, `CARRY FORWARD` |
| **Secondary** | `--carbon` text, 1px `--carbon` underline on hover only, trailing `›` | `Log price change ›`, `Open GPay mandates ›` |
| **Tertiary / nav** | `--ink-faint` text, no underline, leading `‹` or trailing `›` | `‹ Register`, `Burn summary ›` |

Body text, amounts, dates, and settled stamps are NEVER `--carbon`.
Carbon means "this responds to you" and nothing else.

### 8.2 States — required for every interactive element
- **Rest** — as specced.
- **Hover** (pointer only) — `--paper-band` fill on rows and buttons; opacity
  .85 → 1 on actionable stamps. No layout shift except the stamp's 1px rise.
- **Focus** — 2px `--carbon` ring, 2px offset, always visible, never removed.
  Tab order follows the register's reading order: header → rows top-to-bottom →
  totals → right page → rubric.
- **Active/pressed** — 1px downward translate, no colour change.
- **Selected row** — `--paper-band` fill plus a 3px `--carbon` bar in the left
  binding margin. Required, because keyboard actions operate on the selection.
- **Disabled** — `--ink-faint` at .5, no pointer. Prefer hiding over disabling.
- **Loading** — the row's amount cell shows `· · ·` in `--ink-faint`. No
  spinners; a register does not spin.
- **Empty** — see §7 voice. Every empty state carries the view's primary action.
- **Error** — `--stamp-red` text beneath the offending field, register-terse.

### 8.3 Navigation — the missing layer
- **Persistent register header**, present on every view: `KHATA` (returns to
  the spread) · period selector (`‹ SEP 2026 ›`) · `Burn summary ›` ·
  `Review ›` · `?` · `+ ADD ENTRY` (primary). One ruled line. This is the only
  chrome that persists.
- **Back** — every drill-down (entry detail, resolve, burn, review) opens with
  `‹ Register` top-left as a tertiary control. Browser back does the same thing.
- **Depth is capped at two.** Spread → detail → resolve. Nothing nests deeper.
- **Quick add** is a visible primary control (`+ ADD ENTRY`) in the register
  header AND the `/` shortcut. A shortcut is never the only path to an action.
- **Keyboard shortcuts are discoverable**: a `?` tertiary control in the header
  opens the printed rubric of shortcuts. Shortcuts never gate functionality.

### 8.4 Pane relationship (the spread)
- The right page is a **derived view** of the left, never an independent list:
  it renders exactly those entries carrying actionable stamps, in date order.
- Selecting a row on the left highlights its matching item on the right, and
  vice versa (`--paper-band` on both). One selection model across the spread.
- Resolving an item removes it from the right page and re-renders its left-page
  row in place — the register is the truth, the queue is a lens.

### 8.5 Touch and hit targets
- Minimum target 44×44px. The dotted field's target is the FULL ROW (label +
  leader + value), not the line — the line is only its visual.
- Stamps get 8px of invisible padding to reach the minimum.
- Dense 13px rows are fine on pointer devices; on touch, row height minimum
  48px and the RAIL column folds per §9.

### 8.6 Mobile navigation (corrects the page-turn model)
The "book closes" page-turn works for the register ↔ attention pair ONLY. All
other destinations (entry detail, resolve, burn, review) are full-screen pushes
with `‹ Register` back. A swipe flips between the two register pages; it never
navigates elsewhere.

### 8.7 Predictability rules (acceptance criteria)
1. Anything in `--carbon` responds to interaction. Nothing else does.
2. One primary action per view, visible without scrolling.
3. Every destination has a visible path in and a visible path back.
4. No action is reachable only by keyboard or only by hover.
5. Every interactive element has all states in §8.2 defined before build.
6. The user never handwrites; handwriting is rendered from their choices.

## 9. Quality floor

Responsive to 380px: the book closes to a single page below ~900px (see §3);
below ~480px the RAIL column folds into the particulars line. Visible focus
ring in `--carbon`. All money in integer minor units. Keyboard: `/` focuses
quick-add, `Enter` commits, `S` stamps the selected row (selection is always
visible per §8.2). WCAG AA contrast verified for all ink-on-paper pairs.
