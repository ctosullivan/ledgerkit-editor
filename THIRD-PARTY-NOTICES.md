# Third-party notices

## Runtime dependencies

| Package | Licence | Used for |
|---|---|---|
| [Textual](https://github.com/Textualize/textual) | MIT | TUI framework — pinned `textual==8.2.5` |
| [ledgerkit](https://github.com/ctosullivan/ledgerkit) | MIT (currently) | hledger journal parsing/model — pinned `ledgerkit==1.0.0.dev1`. Upstream has relicensed to GPL-3.0-or-later on its `main` branch (not yet released); this table will be updated when the pin is bumped to that release. |

MIT is compatible with GPL-3.0-or-later in the direction used here (a
GPL-licensed program may depend on an MIT-licensed library).

## Directly-translated or adapted material from hledger/Ledger

None. ledgerkit-editor is an editor UI around the `ledgerkit` library; all
hledger format parsing and modelling is delegated to that dependency. Local
utilities (`utils/date_parser.py`, `utils/query_match.py`, etc.) are original
implementations, not translated from hledger/Ledger source.
