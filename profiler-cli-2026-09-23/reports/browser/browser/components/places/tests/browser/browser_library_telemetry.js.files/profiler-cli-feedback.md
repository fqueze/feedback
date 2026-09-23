## Default thread of a loaded profile is not the parent main thread

- Command: `profiler-cli load <taskcluster url of profile_browser_library_telemetry-2.js.json> --session browser-browser_library_telemetry.js-2`, then `profiler-cli thread markers --session ... --category Test --search browser_library_telemetry --list --limit 0`
- Expected: the load selects the parent process GeckoMain (it did for the first profile of the same job), so the test log is listed.
- Got: the session selected `t-17 (GeckoMain, Privileged Content)`, and the markers query answered "0 markers ... No markers match", which reads like the test log is absent.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## Exact DOM event types

- Question: "the popupshowing/popupshown/popuphiding/contextmenu/command/activate/deactivate DOMEvents in this range".
- Command: `thread markers --search "eventType:command,eventType:activate,..." --list --limit 0`
- Got: `eventType:command` also matches every `commandupdate`, `eventType:activate` also matches `deactivate`, so the list was 69 KB of noise; filtered with grep on ` command - `.
- Could have shown: an exact-match form of `field:value` (e.g. `eventType:=command`).
