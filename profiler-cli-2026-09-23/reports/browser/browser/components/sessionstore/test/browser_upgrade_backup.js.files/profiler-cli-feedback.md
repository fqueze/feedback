## Daemon dies silently on a broad `--list --limit 0 --json` over a 2.27M-marker thread

- Command: `profiler-cli thread markers --session browser-browser_upgrade_backup.js-1 --search 'Preference Read' --list --limit 0 --json` (no zoom; parent main thread holds 2,275,737 markers)
- Expected: either the list, or a refusal/truncation saying the result is too large.
- Got: empty stdout (JSON parse failed), and the session was gone on the next call ("Unknown session ... no metadata found"). The daemon log ends with "Received message: command" and no error.
- Workaround: reload the profile (~minutes) and always `zoom push` before listing.
