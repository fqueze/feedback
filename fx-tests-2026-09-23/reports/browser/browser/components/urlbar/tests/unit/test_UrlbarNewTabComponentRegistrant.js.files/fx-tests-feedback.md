## `failures --message` finds nothing for a crash signature, without saying where to look
- Command: `fx-tests failures --harness xpcshell --message "child process hang at shutdown"`
- Expected: the tests behind that message (it is issue 1 of `fx-tests test <path>`, a CRASH).
- Got: "No failure matched. Searched 4,982 tests…" — crashes live in `fx-tests crashes`, which the message does not mention.
- Workaround: `fx-tests crashes --harness xpcshell` (19,349 crashes, 503 tests). A hint "this looks like a crash signature; try `fx-tests crashes --signature`" would have saved a call.

## `test --bugs` prints nothing when no bug names the test
- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal output with no bugs section at all; had to check `--json` (`annotatedBugs: null`) to tell "none" from "flag ignored".

## Question: how long had the hung child been alive, and what did macOS say about it?
- Command: `fx-tests crash <task> <minidump> [--json]`
- The raw dump JSON has `process_uptime` (16–19 s here, which dated the GPU process launch to xpcshell startup), `pid`, and `mac_crash_info` ("sandbox denied the right to lookup com.apple.coreservices.launchservicesd"). None of these are in the text or `--json` output; I had to curl the artifact for 7 dumps.
- Could show: process uptime, main module (the output does show it as a frame, but not as "process: GPU Helper"), and mac_crash_info records.
