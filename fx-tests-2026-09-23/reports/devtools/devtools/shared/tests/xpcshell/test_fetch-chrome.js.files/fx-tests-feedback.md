## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_fetch-chrome.js --bugs`
- Expected: a line such as "Bugs: none name this test" (or the searched query).
- Got: exactly the same 19 lines as without `--bugs`, on stdout and stderr; no bug section at all. Indistinguishable from the flag being ignored or the lookup failing.
- Workaround: took the silence as "no bug"; cross-checked against the sibling report for test_fetch-http.js.
