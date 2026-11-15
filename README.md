# ohh-data

Shared, non-code data for OHH (`.ohh`) tooling: the registry `ohh-ls`
and `tree-sitter-ohh` validate/highlight against, and the test corpus
every OHH-consuming package (grammar, language server, editor
integrations) runs its checks against. Not a standalone tool itself,
every other OHH package expects this repo as a sibling checkout.

## Structure

```
ohh-data/
├── registry/   # JSON facts about the OHH spec: fields, enums, card
│               # format, nested object schemas. Source of truth
│               # `ohh-ls` and the grammar read instead of hard-coding.
└── corpus/     # Sample .ohh files, split into valid/ and invalid/,
                # used as test fixtures by tree-sitter-ohh, ohh-ls/
                # ohh-check, and the editor packages' own test suites.
```

See `registry/README.md` and `corpus/README.md` for what each file
inside covers and how confident each fact/fixture is.
