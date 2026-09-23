## Question: which process is this minidump from, and how long had it been running?

- Command: `fx-tests crash fpZT8fmJRRKBsPtvvGeEiA.0 524e7c53-bd1a-416f-b90b-c7c7dce0f44c --all-threads`
- Expected: the pid, the process uptime and the process type in the header. For a "child process hang at shutdown", the pid is what ties the dump to the harness's `Process <pid> hanging at shutdown` line. The uptime says whether the child had just been launched.
- Got: signature, type, address, OS and CPU count only.
- Workaround: curled `public/test_info/<id>.json` and read `pid` (21484) and `process_uptime` (21 s) with a script.

## Question: how widespread is this failure message tree-wide?

- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests`
- Expected: the tests failing with it, or a pointer to `fx-tests crashes --signature`, since this string is a crash signature.
- Got: "No failure matched", which reads as "this happens nowhere". `fx-tests crashes` then showed 19,591 crashes in 507 tests with that signature.
- Workaround: `fx-tests crashes --harness xpcshell`.

## `fx-tests test <path> --bugs` said nothing when no bug matched

- Command: `fx-tests test devtools/client/webconsole/test/xpcshell/test_javascript_logging.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: output identical to the run without `--bugs`, so it was unclear whether the search had run.
