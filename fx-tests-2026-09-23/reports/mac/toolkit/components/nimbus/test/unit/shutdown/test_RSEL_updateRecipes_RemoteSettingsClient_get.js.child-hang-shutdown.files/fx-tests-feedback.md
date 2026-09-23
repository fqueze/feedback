## "Which child process hung, and where, across every dump of one failure mode"

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 2`, then `fx-tests crash <task> <dump> --all-threads --frames 0` once per dump (39 times), with a shell loop grepping the module name (`Firefox Nightly GPU Helper`) and the main thread's Gecko frames.
- Expected: one command answering "what process type hung, and in which Gecko frame" for all dumps of a failure mode, e.g. `fx-tests test <path> --issue 2 --dumps-summary` grouping dumps by process type (from the main module name) and by the innermost Gecko frame of the main thread.
- Got: one dump at a time; the per-dump summary also does not name the process type (it has to be read off frame 27, the helper binary).
- Workaround: the loop, about 40 `fx-tests crash` calls.

## `--bugs` with no bug found prints nothing about bugs

- Command: `fx-tests test toolkit/components/nimbus/test/unit/shutdown/test_RSEL_updateRecipes_RemoteSettingsClient_get.js --bugs`
- Expected: a "Bugs" block, or a line saying none names the test.
- Got: the same output as without `--bugs`; no way to tell "searched, none" from "flag ignored".
- Workaround: Bugzilla REST search by summary.
