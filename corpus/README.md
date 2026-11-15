# OHH corpus

Shared by `tree-sitter-ohh`,
`ohh-ls`/`ohh-check`, and (later) the editor-side tests, same role
`phh-data/corpus` plays for PHH.

## `valid/`

| File | What it exercises |
|---|---|
| `holdem-cash-pokerstars.ohh` | The real example hand fetched from the spec site itself (hh-specs.handhistory.org, "Holdem cash hand (PokerStars)"), 9-handed fast-fold cash hand, preflop all-in, showdown. Exercises optional-field omission patterns found in a real tool's output: no `display`/`is_sitting_out` on players, no `description` on actions, `cards` present-but-empty on `Preflop` vs. absent entirely on `Showdown`. |
| `multi-hand.ohh` | Two synthetic minimal hands, each a complete valid `{"ohh": {...}}` object, separated by exactly one blank line, per the storage spec's own convention. Pins down that a multi-hand `.ohh` file is **not** one JSON document, generic JSON parsers must fail on this file while `tree-sitter-ohh`'s document-root rule (repeat of objects) must not. |
| `run-it-twice.ohh` | Synthetic hand with the `Run_It_Twice` flag and two `Flop`/`Turn`/`River` triples sharing street names, pins down that `street` is not unique within `rounds` and that `round_obj.id` (not array position, not street name) is the authoritative order. |

**Not yet added** (not yet called done): an Omaha
example and a tournament example, to exercise `tournament_info`/
`tournament_bounties`/multi-card-per-player hands, the fetched spec site
has these pages but they were not pulled into this session's research.

## `invalid/`

One fixture per rule, same discipline as `phh-data/corpus/README.md`, each
file violates exactly one registry rule:

| File | Violated rule |
|---|---|
| `missing-required-field.ohh` | `bet_limit` (required per `ohh-data/registry/fields.json`) is absent entirely. |
| `bad-enum-value.ohh` | `game_type` is `"Omahaa"`, not a member of `ohh-data/registry/enums.json#game_type`. |
| `bet-in-preflop.ohh` | A `"Bet"` action appears in a `"Preflop"` round, the spec's own stated exception ("Bet is not a valid preflop action"), recorded in `enums.json#action.notes`. |

**Deliberately not added**: a "malformed multi-hand separator" fixture
(no blank line, or more than one, between hands). Unlike PHH's
corpus-backed rules, this is not a structural parse error under
`tree-sitter-ohh`'s grammar (whitespace between two back-to-back JSON
objects is just whitespace to a JSON-shaped grammar, two objects with
zero or two blank lines between them still parse as two root-level
objects without error). Enforcing the spec's "exactly one blank line"
convention would be a style-lint, not a syntax or field-validation rule,
and is out of scope for `ohh-ls`'s current L1 checks, noted here rather
than silently dropped.

## Not independently verified against a second implementation

Unlike `phh-data/corpus` (every fixture's pass/fail status confirmed by running
it through vendored, runnable PokerKit), nothing here has been run through
a second OHH-consuming tool, none is vendored in this project yet.
The `valid/holdem-cash-pokerstars.ohh`
fixture is at least byte-sourced from the spec site's own published
example, which is the strongest grounding available right now; the other
two `valid/` fixtures and all three `invalid/` fixtures are hand-built
against the registry, not independently cross-checked.
