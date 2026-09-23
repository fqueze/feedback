## `fx-tests test <path> --bugs` shows no bug

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_status-bar.js --bugs`
- Expected: bug 1819998 ("Intermittent devtools/client/netmonitor/test/browser_net_status-bar.js | single tracking bug", REOPENED, annotated 4 times in the window).
- Got: the same output as without `--bugs`, no bug section.
- Workaround: Bugzilla REST quicksearch on the file name.

## Question: "the scope-variables dump of this failure"

- `fx-tests task <taskId> --profiles` lists the per-test profile, but not the `public/test_info/scope-variables-<Date.now()>-<file>_<line>_<col>.json` artifact the same failure uploaded. Here that dump was the decisive evidence (it holds the status bar's rendered "load: 1 ms"), and I only found it by listing the task's artifacts with curl.
- It could be printed next to the profile URL for each failure, as the profile is.
