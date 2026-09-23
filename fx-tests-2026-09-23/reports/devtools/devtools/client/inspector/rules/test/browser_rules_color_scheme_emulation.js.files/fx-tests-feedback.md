## `test --bugs` printed no bugs section at all
- Command: `COLUMNS=200 fx-tests test devtools/client/inspector/rules/test/browser_rules_color_scheme_emulation.js --bugs`
- Expected: a Bugs section, or an explicit "no sheriff-annotated bug names this test".
- Got: the same output as without `--bugs`, with nothing on stderr either. I could not tell "no bug" apart from "the lookup silently failed".
- Workaround: queried the Bugzilla REST API by summary (no bug exists).
