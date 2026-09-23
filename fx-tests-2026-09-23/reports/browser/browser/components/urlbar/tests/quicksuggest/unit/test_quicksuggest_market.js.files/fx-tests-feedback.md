## `fx-tests test <path> --bugs` prints no bug section when none is found
- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_market.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, so I couldn't tell "no bug" apart from "the flag was ignored".
- Workaround: searched Bugzilla REST by summary.

## `fx-tests failures --message` does not search crash signatures, and doesn't say so
- Question: "how many tests hit `child process hang at shutdown`?"
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a pointer to `fx-tests crashes`.
- Got: `No failure matched. Searched 4,982 tests…`, which reads as "this signature is specific
  to my test". `fx-tests crashes --harness xpcshell` then showed 19,349 crashes in 503 tests.
- Suggestion: when `--message` matches nothing in failures but matches a crash signature, say so;
  and have `crashes` take `--message`/`--tests` to list the tests behind one signature.

## `fx-tests crash --all-threads` repeats identical stacks
- Command: `fx-tests crash LtzWWLsdQga6nv0JV6ne7Q.0 BFAA6A55-23C4-44DC-ABAA-4CAD248FF0BE --all-threads --frames 15`
- Got: 670 lines, most of them 12 WRWorker/WRWorkerLP threads with the same rayon sleep stack, each
  printed in full with every inlined std frame.
- Suggestion: collapse threads whose stacks are identical ("WRWorker#0-3, WRWorkerLP#0-7: same
  stack"), and optionally hide inlined `std::`/rustlib frames.
