# DECISIONS.md — append-only log

One line per decision: date · what · why · what was rejected.
Never edit past entries; correct by appending a new one.

---

## Decided

- **2026-08 · Problem framing locked.** Cross-rail subscription ledger for an
  Indian power user; discovery/tracking/cancellation gap; no-bank-credentials
  stance. Rejected: generic budgeting-app framing (crowded, wrong persona).
- **2026-08 · Visual direction = A (bahi/passbook ledger).** Domain-native
  nostalgia; aesthetic carries information. Rejected: B railway ephemera
  (metaphor strains), C CRT terminal (over-done), D print ephemera
  (decoration without grammar).
- **2026-08 · DESIGN v1.1: bahi spread amendments** (from owner's real bahi
  artifact). Warm paper + madder red rules; two-page spread (record left,
  attention right); ditto marks for bundle children; boxed double-rule C/F;
  "book closes" responsive model. Rejected: photorealism (wood, stains, page
  curl, 3D binding), handwritten data voice.
- **2026-08 · Two reds, two jobs.** Structural madder (`--rule-red`) vs
  semantic stamp red (`--stamp-red`). Rejected: single red (grid would cry
  wolf).
- **2026-08 · UI machinery: no component libraries, no Tailwind.** Bespoke
  ~8-component set from `tokens.css`; headless primitives (Radix) allowed for
  hard behavior only, fully reskinned. Rejected: shadcn/ui (default aesthetic
  = the exact look this product rejects; would become every build session's
  default), Tailwind (token names beat utility classes for designer-legible
  diffs on a solo bespoke project).
- **2026-08 · Build tool = Claude Code desktop app.** Owner builds without
  tech background; CLAUDE.md/DESIGN.md double as its native project config.
  Studio (chat project) decides; workshop (Claude Code) executes.
- **2026-08 · Spec sync = GitHub (Option A).** Studio pushes spec updates to
  github.com/aygupta-coursera/subscription-management; workshop pulls before
  build sessions. Rejected: manual download-and-drag (drift-prone, already
  caused one stale-spec incident), Drive-synced folder (laggy, no history).
- **2026-08 · DESIGN v1.2: report-card steals adopted.** Provenance rule
  (machine facts print in Doto; the form speaks Plex; owner judgments write
  in Kalam — extends pencil/ink entry states); dotted leader-line fields
  (no boxed inputs); one-line rubric footer; period-close verdict ritual
  ("Keep for {MMM}? —" answered in Kalam, logged as a dated judgment).
  Rejected: period-serif form labels (a fourth type voice with no role;
  walks toward the cream+serif cliché).
- **2026-08 · DESIGN v1.3: interaction layer added after usability pass.**
  Frames read as documents, not an interface: controls indistinguishable from
  labels, no navigation, no states, unclear pane relationship. Added §8
  (control hierarchy: one primary per view, carbon means interactive and
  nothing else; required states incl. selected and focus; persistent register
  header with visible back; right page is a derived lens on the left; 44px
  targets; depth capped at two). Corrections: (a) red is scarce — routine
  amounts print in `--ink`, `--stamp-red` reserved for danger/≤7 days/errors,
  because colouring every debit red made the alarm meaningless; (b) stamps are
  actionable (solid border, pointer, `›`) or settled (dashed, .6, inert) —
  never ambiguously both; (c) Kalam is an output style, never an input method —
  the owner taps Keep/Cancel and the answer renders as handwriting.
  Rejected: page-turn as a general navigation model (works only for the
  register ↔ attention pair), shortcut-only actions.

## Proposed, not yet folded into specs

- **CLAUDE.md data-ingestion section:** signal ladder (statement-CSV
  recurrence detection in v1 → email receipts v2 → SMS via companion/forwarder
  v3 → quarterly rail audits), sync as a reconciliation loop (propose →
  confirm), pencil-vs-ink entry states, Account Aggregator noted as the
  productization-only path. Would move CSV recurrence detection INTO v1 scope.
- **WORKFLOW.md:** two-rooms working method (studio/workshop), the lap cycle,
  reference ritual, weekly dogfood review.

## Open questions (tracked in CLAUDE.md §9)

Name; v1 alert channel; local vs always-on server; Gmail OAuth vs
forward-to-address.
