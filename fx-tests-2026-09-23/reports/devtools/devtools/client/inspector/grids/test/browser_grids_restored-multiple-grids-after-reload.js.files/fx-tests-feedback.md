## Question: "which bugs name this test?"

- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_restored-multiple-grids-after-reload.js --bugs`
- Expected: a "Bugs" section, saying "none" when no bug is annotated.
- Got: the normal output with no bugs section at all; only `--json` showed `annotatedBugs: []`. Silence reads the same as "the flag was ignored".
- Workaround: `--json` and read `annotatedBugs`.
