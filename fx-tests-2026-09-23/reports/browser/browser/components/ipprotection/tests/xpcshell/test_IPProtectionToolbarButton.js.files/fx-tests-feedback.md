## `failures --message` silently excludes crashes (browser-test_IPProtectionToolbarButton.js)

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests sharing that message (it is the #1 issue `fx-tests test` printed for the test).
- Got: `No failure matched. Searched 4,982 tests ... Check --path, --message, --component for typos.`
- Workaround: `fx-tests crashes --harness xpcshell` has it (19,349 crashes, 503 tests). The "no match" hint could say that CRASH messages live under `crashes`, since `test`'s Issues list mixes both.
