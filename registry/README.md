# OHH registry

Data, not code. `ohh-ls`, the grammar's test
tooling, and docs/hover text should all read these files rather than
hard-coding OHH facts.

| File | Contents | Sourced from |
|---|---|---|
| `fields.json` | Top-level hand-object fields, types, requiredness, doc text | https://hh-specs.handhistory.org/json-object/ pages, fetched 2026-11-04, spec_version 1.4.9 |
| `objects.json` | Nested object schemas (`player_obj`, `round_obj`, `action_obj`, `pot_obj`, `player_wins_obj`, `tournament_info_obj`, `speed_obj`, `tournament_bounty_obj`, `bet_limit_obj`) | Same per-object pages |
| `enums.json` | `game_type`, `bet_type`, `street`, `action`, `currency` non-ISO extensions, hand `flags`, tournament `flags`, exact string values and descriptions | Same, plus the dedicated `flags`/`currency`/etc. pages |
| `cards.json` | Rank/suit tables; explicitly flags the unknown-card-marker question as unresolved | https://hh-specs.handhistory.org/string-formats/card.md |

## Honest confidence note, read before trusting this registry the way `phh-data/registry` is trusted

`phh-data/registry` was cross-checked field-for-field against vendored, runnable
`pokerkit/` source. **This registry has no equivalent cross-check.** Every
fact here came from reading the spec's own prose pages, not from running a
second, independent OHH-consuming implementation against it. `rs-poker`
is the leading candidate for that role and has not been vendored or
checked yet. Treat every `required: true` in
`fields.json`/`objects.json` as "the spec's stated global default, no
stated exception found in the pages read" rather than "confirmed against a
second source," and treat fields marked `required: false` with a
"not present in the only example fetched so far; requiredness unconfirmed"
note as genuinely open questions, not settled facts.

## One real correction found by running the validator, not by inspection

`fields.json` originally marked `tournament`, `tournament_info`,
`table_handle` and `table_skin` as `required: true`, following the spec's
stated global default literally. Running `ohh-check` against
`ohh-data/corpus/valid/holdem-cash-pokerstars.ohh`, the one fixture sourced
directly from the spec site's own published example, not hand-built, immediately flagged all four as missing. Rather than treat the spec's
prose as more authoritative than its own worked example, all four are now
`required: false`, each with a note recording exactly this. This is the
same discipline `phh-data/corpus` used to catch grammar and fixture bugs: the
corpus is a check on the registry, not just a demo of it.

## Known gaps / next entries needed

- `currency`'s ISO 4217 allowlist itself is not vendored here (only the two
  known non-ISO extensions, `XPM`/`XSC`), a real validator needs the full
  ISO 4217 list from somewhere, not just these two exceptions.
- `tournament_info_obj.type`'s enum values were not confirmed (page not
  fetched in full).
- `speed_obj` was read from the object index only, not its own dedicated
  page, its two fields' exact semantics (especially `round_time`'s unit)
  are unconfirmed.
- The unknown/hidden-card representation (`cards.json`) is a named open
  question, not a filled-in fact.
- `start_date_utc`'s exact format (both at hand level and
  `tournament_info_obj` level) is given only by example, with no explicit
  grammar/format statement read yet.
