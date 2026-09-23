## Question: on which configs does failure mode N happen, with counts?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 2`
- Expected: a per-config count for the chosen issue (like the "Failing configurations" table, restricted to that issue), since the table printed above the task list is for all issues together and the worst config overall is not the worst config of each mode.
- Got: the all-issues config table, then the issue's task IDs grouped by date with one config name per line.
- Workaround: counted the 35 task lines by config by hand (and noticed by eye that none were asan, tsan, debug or mac, which mattered for the diagnosis).
