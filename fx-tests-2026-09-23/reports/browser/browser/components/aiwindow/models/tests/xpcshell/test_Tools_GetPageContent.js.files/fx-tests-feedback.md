## `test --bugs` prints nothing when no bug is found
- Command: `fx-tests test browser/components/aiwindow/models/tests/xpcshell/test_Tools_GetPageContent.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: exactly the output without `--bugs`; nothing on stderr either. "No bug" is indistinguishable from "the flag did nothing".
- Workaround: Bugzilla REST quicksearch by hand.

## `failures --message` does not match crash signatures, and does not say so
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message, or a hint that crashes live under `fx-tests crashes`.
- Got: "No failure matched", while `fx-tests crashes --harness xpcshell` lists that signature first (19,349 crashes, 503 tests).
- Workaround: `fx-tests crashes`, which has no `--tests` to list the tests behind a signature.

## Question: "in which process and on which thread was the hung child stuck, across all dumps of this test?"
- Command: a loop over the 74 `fx-tests crash <task> <dump> --raw` of `fx-tests test <path> --task-ids`, parsed with Python (main module name, crashing-thread Gecko frame, any thread in `_LSBundleFindWithNode`, `mac_crash_info` message).
- The default `fx-tests crash` output could have shown: the process type / main module (here "Firefox Nightly GPU Helper", only visible as a bottom frame), the `mac_crash_info` records (here LaunchServices' "sandbox denied the right to lookup com.apple.coreservices.launchservicesd" — the key clue, absent from the default output), and pid / process uptime.
- A per-test aggregation ("N dumps: M with main thread in X, K with thread 'Renderer' in Y") would have answered it in one command.
