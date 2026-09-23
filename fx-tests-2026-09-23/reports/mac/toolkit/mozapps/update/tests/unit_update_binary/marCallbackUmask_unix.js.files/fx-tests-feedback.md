## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marCallbackUmask_unix.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, with no mention of bugs at all, so "no bug" and "the flag did nothing" look the same.
- Workaround: a Bugzilla quicksearch for the file name.

## Question: "which failing runs have a per-test profile small enough to load?"

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --profiles`
- Expected: the size of each profile artifact next to its URL. Most of this test's per-test profiles are 48–65 MB gzipped, and profiler-cli dies loading those.
- Got: URLs only.
- Workaround: a HEAD request on each of the 54 artifacts, reading `x-goog-stored-content-length`.
