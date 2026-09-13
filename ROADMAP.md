# Roadmap

The project is in **early beta**. A milestone is only marked `[DONE]` on explicit
user instruction — never inferred by Claude.

---

## What Is Shipped (v1.2.0)

The editor is a full-screen, keyboard-driven plain-text hledger journal editor.

**Editing surface**
- Full-width `TextArea` editing surface (`JournalEditor`)
- hledger syntax highlighting: dates, flags, payees, accounts, amounts, commodities,
  comments, directives — including field-level highlighting for `P` price
  directives (date/commodity/rate each coloured separately, not one flat colour)
- Monokai Pro as default app theme; CSS-variable bridge enables runtime theme switching
- Enter auto-indent on transaction header and posting lines

**File operations**
- `Ctrl+S` — sort by date, align posting amounts to column 52 (emacs ledger-mode
  style), save; merges active view-filter edits before writing
- File-path bar with modified indicator

**Undo / redo**
- `Ctrl+Z` / `Ctrl+Y` — two-layer undo stack: `CommandHistory` (Layer 2, text +
  model operations) consulted first; falls through to TextArea native `EditHistory`
  (Layer 1, free-form text + `Ctrl+G` duplicate)
- `atomic_edit()` collapses bulk operations (e.g. multi-transaction `Ctrl+R`) into
  a single `Ctrl+Z` press

**Transaction editing**
- `Ctrl+R` — 3-state cleared cycle (uncleared → pending `!` → cleared `*`);
  bulk-toggle for multi-block selections (all cleared → all uncleared; otherwise →
  all `*`); fully atomic — single `Ctrl+Z` reverses the entire bulk toggle
- `Ctrl+G` — duplicate current transaction block to end of file with today's date;
  multi-block selection duplicates all selected blocks; fully undoable with `Ctrl+Z`
- `Ctrl+D` — insert today's date at cursor
- `Ctrl+T` — select current transaction block; repeated presses extend selection
- `Shift+Up` / `Shift+Down` — increment/decrement the date sub-field (year/month/day)
  under the cursor, on either a transaction header or a `P` price-directive date;
  an unpadded date (`2026-9-1`) is expanded to zero-padded form (`2026-09-01`) as
  part of the same keypress; falls through to text selection off a date field

**Navigation & search**
- `Ctrl+F` — incremental search bar with match highlighting (`N of M` counter)
- `Ctrl+C` while the search input has focus — copies the current match's text
  (a real selection made inside the search box itself still copies normally)
- `Shift+PgUp / Shift+PgDown` — navigate previous/next transaction header, or
  previous/next match when the search bar is open; keeps a 4-line context
  margin above *and* below the cursor in both directions
- `Ctrl+Home / Ctrl+End` — cursor to start/end of file
- `Ctrl+A` — select all

**View filter**
- `Ctrl+L` — cycle view: All transactions → Cleared only → Unreconciled only → All
- Edits made in a filtered view are merged back into the full journal on save or
  filter change

**Transaction filter**
- `Ctrl+O` — transaction filter popup: Date From/To (smart dates, quarters,
  named periods, relative offsets, month-name/year-month shorthand),
  Account/Payee (substring-or-regex, the same convention hledger itself
  uses). Combines with the `Ctrl+L` cleared/uncleared cycle — both narrow
  the view together rather than one overriding the other. Status bar shows
  a visible/total transaction count whenever any filter is active. Field
  text and any applied filter persist across a popup close/reopen. Shipped
  in v1.1.0; Tab-on-empty-field and field-persistence fixes in v1.1.1.

**Tab autocomplete**
- `Tab` — completes account names (posting lines) and payee names
  (transaction headers) from declared directives plus everything already
  used in the journal; shell-style cycling through matches. Also available
  in the `Ctrl+O` popup's Account/Payee fields (field value cycles in
  place, no suggestion bar there). Suggestions rebuild from a snapshot on
  file load and after each `Ctrl+S`. Shipped in v1.1.0.

**Other**
- `Ctrl+P` — command palette

---

## Internal — `release/1.1.0`

`widgets/transaction_table.py` was split into `JournalEditor` + four mixins
(Module Size Rule) as part of building the transaction filter and Tab
autocomplete above — no user-visible change. See
`planning/next-release-phase-plan.md` for the full phase breakdown.

---

## Window Panes — Removed in v0.8.0

The `BalanceSidebar` (account tree view), `RegisterPanel` (posting history), and all
reconcile-mode machinery (`ReconcileMixin`, `ReconcileStatusBar`, `ReconcileSummary`,
`ReconcileActions`) were removed in v0.8.0. The app is now a pure text editor with
no side panels.

These features are not planned to return unless explicitly requested.

---

## Upcoming Milestones

### Milestone A — Transaction Filter (Ctrl+O)

Implemented in Phase 3 of `planning/next-release-phase-plan.md` and shipped
in v1.1.0, with follow-up fixes in v1.1.1. All checklist items below are done.

- [x] Assemble `ledgerkit.Query` from date-from, date-to, account, payee fields
- [x] Smart date parsing: "last month", "ytd", "q1", ISO 8601, relative offsets
- [x] Apply filter: show only matching transactions in the editor
- [x] Clear filter restores full journal

### Milestone B — Autocomplete & Templates

Phase 4a (name completion) implemented in `planning/next-release-phase-plan.md`
and shipped in v1.1.0. Phase 4b (`Alt+P`/`Alt+N` — historical-account/template
suggestion) was deliberately deferred there to keep scope bounded; still open.

- [x] Tab autocomplete from declared accounts + all posting accounts in the loaded journal (and declared/used payees, for the transaction header)
- [ ] `Alt+P` / `Alt+N` — insert previous/next matching transaction template
  (Emacs `ledger-mode` convention)

### Milestone C — Emacs Ledger-mode Date Editing

- [x] `Shift+Up / Shift+Down` — increment/decrement date by one day (shipped
  v1.0.0; extended to `P` price directives and unpadded dates in v1.0.2 —
  see "What Is Shipped" above)
- [ ] `Shift+Alt+Up / Shift+Alt+Down` — increment/decrement date by one month
- [ ] `Ctrl+K` — delete to end of line

### Milestone D — Performance & Polish

- [ ] Large journal support (10 000+ transactions without UI lag)
- [ ] Command palette: register all named actions
- [ ] Configurable keybinding profiles (MS Office / Emacs Ledger-mode stubs are in
  `src/ledgerkit_editor/keybindings/`)
