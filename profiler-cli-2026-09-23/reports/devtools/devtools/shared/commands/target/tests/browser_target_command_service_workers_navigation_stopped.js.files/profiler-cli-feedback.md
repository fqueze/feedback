
## review: `--search` does not match the label `--list` prints (2026-09-22)

- Command: `profiler-cli thread markers --session <s> --search 'PASS — devtools/shared/commands/target/tests/browser_target_command_service_workers_navigation_stopped' --list --limit 0` on a resource-usage profile.
- Expected: the `test` marker whose listed label is exactly `PASS — devtools/.../browser_target_command_service_workers_navigation_stopped.js`.
- Got: 0 markers. Searching `navigation_stopped` alone finds it; the `PASS — ` prefix is the status field joined into the label, and search does not cover that joined form.
- Workaround: search the test path only. Cost one extra call; a reviewer naturally pastes the label a report quotes.
