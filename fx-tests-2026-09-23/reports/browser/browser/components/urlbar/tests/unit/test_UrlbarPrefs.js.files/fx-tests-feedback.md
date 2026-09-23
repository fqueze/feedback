## Question: "the tests behind this crash signature"

- Command: `fx-tests crashes --harness xpcshell --path browser/components/urlbar/tests/unit --signature "child process hang"`
- Expected: the 4 tests the row counts, as `failures --tests` does (automatic at 3 rows or fewer there).
- Got: `317  4  0  child process hang at shutdown` with no test list; `crashes` has no `--tests` flag.
- Workaround: `--json`, read `rows[].tests`.

## `fx-tests test <path> --bugs` printed no bug section at all

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_UrlbarPrefs.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`, so it is unclear whether it searched and found none.
- Workaround: searched Bugzilla REST by hand.

## Question: "which process is this minidump of?"

- Command: `fx-tests crash <task> <dump>` (for 23 dumps of the same signature).
- Expected: the process type (GPU, content, utility...) in the header, since a shutdown-hang dump is always of a child.
- Got: only visible as the frame `Firefox Nightly GPU Helper + 0x6b5` near the bottom of the main thread, which needed `--frames 0` and a grep.
- Workaround: loop over `fx-tests crash ... --frames 0` and grep for the helper binary name.
