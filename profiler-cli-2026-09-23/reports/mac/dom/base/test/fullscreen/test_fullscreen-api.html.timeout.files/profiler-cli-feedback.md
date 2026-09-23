## Log markers (mochitest INFO) print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session tfa-timeout-1` (thread t-13, content GeckoMain of task ajIfcoMXQIK0m0cfD5JKyw's `profile_test_fullscreen-api.html.json`), then `profiler-cli marker info m-5892`.
- Expected: the INFO rows carry the test's `info()` text, e.g. `Waiting for focus to return to main window`.
- Got: `[(empty)] INFO: (empty)` on every INFO row; `marker info` shows `Level: (empty)`, `Message: (empty)`. `--json` has `"value": "Waiting for focus to return to main window", "formattedValue": "(empty)"`. Same issue already logged by the sibling `test_fullscreen-api.html` report.
- Workaround: `--list --json` and a script printing `data.message`.
- Question the default output should have answered: "which subtest was running, and what was the test waiting for, when it timed out".

## DOMEvent markers do not say which window a `window` target is

- Question: "which window got this `focus - window` / `activate - window` event?" — the test has a main window and a popup in the same content process (and the parent has a chrome window per top-level window).
- Command: `profiler-cli thread markers --search "eventType:focus,eventType:activate" --list --limit 0 --session tfa-timeout-1` on the content main thread t-13, then `marker info m-5943 m-5945 --json`.
- Got: `target: window`, `latency`, `eventType` only; no inner window ID, URL or browsing context, and no stack.
- Workaround: on the parent main thread, the `focus - browser@<address>` rows that follow each `activate` identify the tab's `<browser>`; matched the address against the one focused after each normal popup close. Cost several extra queries per profile.
- What would have answered it: the document URL or innerWindowID of the event target in the DOMEvent payload, or in the list row.
