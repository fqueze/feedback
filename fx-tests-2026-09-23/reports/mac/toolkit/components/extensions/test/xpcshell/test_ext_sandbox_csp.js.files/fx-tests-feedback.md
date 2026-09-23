## `fx-tests test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_sandbox_csp.js --bugs`
- Expected: a "Bugs" section, saying "none found" when the search came back empty.
- Got: the same output as without `--bugs`; no section at all. Only `--json` shows `"annotatedBugs": []`, and even that does not say whether a Bugzilla search for the file name was made.
- Workaround: read `--json` and assume empty means none.
  - Follow-up: a bug does exist. `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=test_ext_sandbox_csp"` finds bug 2064681, "Intermittent toolkit/components/extensions/test/xpcshell/test_ext_sandbox_csp.js | single tracking bug" (NEW, filed 2026-08-19 from comm-central). `--bugs` missed it: its summary names the exact path. Possibly because its only annotations are on comm-central (`fx-tests intermittent --bug 2064681 --tree all --since 30`: 6, all comm-central), but `--bugs` is documented as finding "any that names the test".

## Question: "the gap between marker A ending and marker B starting, in each of N threads"

- Commands: `thread select t-N` then `thread markers --search "Msg_SetProcessSandbox,Msg_ConstructBrowser" --list --json`, looped over 21 content-process threads, with a script computing `ConstructBrowser.start - (SetProcessSandbox.start + duration)`.
- The default output could answer the first half with a cross-thread marker search: `profile markers --search` finds which threads have a marker, but a per-thread duration table for one marker name (one row per thread: start, duration) would have answered "how long does this runnable take in every content process" without a loop.

(The section above, "the gap between marker A ending and marker B starting", is about profiler-cli, not fx-tests; filed here by mistake and copied to profiler-cli-feedback.md.)
