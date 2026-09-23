## `field:value` search has no exact match

- Question: "is there any `command` DOMEvent on the parent main thread?" (as opposed to `commandupdate`)
- Command: `profiler-cli thread markers --search "eventType:command" --list --limit 20 --session review-browser_shortcutkey_ctrlf5_prevent_default.js-1`
- Expected: a way to match the field value exactly, e.g. `eventType:=command`.
- Got: 234 markers, all `commandupdate`, since the value matches as a substring; the one real `command` event was buried.
- Workaround: `--search "eventType:command,-eventType:commandupdate"`, which only works once you know every longer value to exclude.
