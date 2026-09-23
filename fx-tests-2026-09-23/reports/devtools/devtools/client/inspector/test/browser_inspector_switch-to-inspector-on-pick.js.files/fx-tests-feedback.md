## `--bugs` gives no answer when no bug is found (browser_inspector_switch-to-inspector-on-pick.js)

- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_switch-to-inspector-on-pick.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: the same output as without `--bugs`, with no bug line at all, so "no bug" looks the same as "the flag was ignored or the lookup failed".
- Workaround: assumed there is no bug; nothing in todo.md contradicted that.
