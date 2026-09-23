## The configs behind one failure mode (browser_closed_objects_changed_notifications_tabs.js)

- Question: on which configs does failure mode N (`--issue N`) happen, with counts? Here issue 3 ("Got 4, expected 5") turned out to be mostly `-no-nv`, which the overall per-config table cannot show.
- Command: `fx-tests test <path> --task-ids --limit 0 --issue 3`
- Expected: a per-config count table for that issue, like the "Failing configurations" table but restricted to the issue.
- Got: the overall per-config table (all issues), then a flat task list for the issue.
- Workaround: `... --issue $i | rg -o "test-[a-z0-9-]+/[a-z]+-mochitest-browser-chrome-[a-z0-9-]*[a-z]" | sed -E 's/-[0-9]+$//' | sort | uniq -c` per issue.

## `--since` takes days, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 1 --since 2026-09-18`
- Expected: failures since that date (the output prints dates everywhere, so a date is the natural input).
- Got: `--since expects a non-negative integer, got "2026-09-18"`.
- Workaround: `--since 3`. Accepting an ISO date too would save the round trip.
