## Question: in what order, to the millisecond, did these Test markers happen, and in which test?

- Command: `profiler-cli thread markers --category Test --search "TEST-UNEXPECTED,watcher' is already destroyed,Entering test" --list --limit 0 --session <id>` on a 1m32s per-test profile.
- Expected: times precise enough to order markers a few ms apart (e.g. "Entering test" at 88.704s, a console error at 88.706s, "Adding a new tab" at 88.706s), and the marker's `Test Name` field, since a per-test profile holds a whole manifest's tests.
- Got: `t=1m29s` for all three, and no test name column; the order and the owning test were only recoverable from `--json` (`start` and `data.test`) through a script.
- Workaround: `--json | python3` printing `start/1000` and `data.test`.

## Review: loading a raw per-test profile URL selects a content-process thread

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/erIj2sHgR0Kp-4Wir081pQ/runs/0/artifacts/public/test_info/profile_browser_resources_several_resources.js.json" --session <id>`
- Expected: the parent process GeckoMain (t-0) selected, where the test log lives.
- Got: `Selected thread: t-20 (GeckoMain, WebExtensions)`; a Test-marker search on it would silently find nothing.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.
