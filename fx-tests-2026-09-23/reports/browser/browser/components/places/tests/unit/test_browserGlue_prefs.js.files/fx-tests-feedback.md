## `fx-tests test <path> --bugs` prints no bug section at all when no bug is found

- Command: `fx-tests test browser/components/places/tests/unit/test_browserGlue_prefs.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the exact same output as without `--bugs`; I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla REST search by hand.

## Question: which tests share one crash signature (`child process hang at shutdown`)

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests behind that message, since `fx-tests test` lists it as the failure message.
- Got: "No failure matched" — crash signatures live under `fx-tests crashes --signature`, not `failures --message`.
- Workaround: `fx-tests crashes --harness xpcshell`.
- What the output could show: the no-match message could point to `fx-tests crashes --signature <text>` when the text matches a crash signature.
