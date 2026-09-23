## `--bugs` prints nothing when there is no bug
- Command: `fx-tests test browser/components/profiles/tests/unit/test_create_profile.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: the usual summary and Issues list, and no mention of bugs at all, so I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla REST search by summary.

## Question: which process and stack this failure mode's minidumps are in
- Commands: `fx-tests test <path> --task-ids --limit 0 --issue 1`, then a shell loop running `fx-tests crash <task> <dump> --frames 60` 56 times and grepping for `nsAppShell::Init`, `RenderThread::ShutDown` and the helper binary name.
- The signature "child process hang at shutdown" hides which child hung (always the GPU Helper here) and where. A per-issue summary would have answered it in one call: the crashing process's binary, plus the dumps grouped by their top in-Gecko frames with counts.

## Question: which process one dump is from (review)
- Command: `fx-tests crash IBJRCfkpSWSkAkzXF_xXqQ.0 <dump> --harness xpcshell --raw` for 8 dumps, and a script reading `modules[main_module].filename` and `mac_crash_info.records[].message`.
- The default text output names neither the dumped binary (`Firefox Nightly GPU Helper`, which is what separates a GPU helper from a content process in a "child process hang at shutdown") nor macOS's crash-info message (here "the sandbox denied the right to lookup com.apple.coreservices.launchservicesd"). One `Process:` line and a `macOS crash info:` line would have answered it.
- Trap in `--raw`: `pid` and `process_uptime` belong to the writer (the parent xpcshell: 1870 and 14 s), not the dumped child (1893). If shown, label them as the parent's.
