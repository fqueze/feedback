## Resource-usage profile: CPU/IO marker payloads need --json (2026-09-22, test_tabnotes_canonicalurl_restore.py)

- Question: "how much was the machine writing to disk, and what was IO wait, in the 500 ms around t=533.2 s?"
- Command: `profiler-cli thread markers --search "name:CPU Use,name:IO" --list --limit 0` after `zoom push 532.6,533.9`
- Got: one row per marker with name/time/duration but no payload (no CPU %, IO wait %, bytes written), so I needed `--json | jq '.flatMarkers[] | .data'` to read them; `marker info` works but only a few handles at a time.
- What would have answered it: `--list` printing the payload fields inline (e.g. `written=16MB iowait=2.5%`) for these markers, or `profile info` showing the resource-usage CPU/IO series as counters with the "over time" buckets, as it does for real counters (this profile reports "No counters" and "No significant activity" although it has 8771 CPU Use markers).
