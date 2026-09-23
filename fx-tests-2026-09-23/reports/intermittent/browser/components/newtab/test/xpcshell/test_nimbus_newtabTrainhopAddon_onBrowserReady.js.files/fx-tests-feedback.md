## Question: since when does one failure mode happen, and on which configs? (test_nimbus_newtabTrainhopAddon_onBrowserReady.js)

- Command: `fx-tests test <path> --history` and `fx-tests test <path> --task-ids --issue 1 --limit 0`
- Expected: a per-day (and per-config) count for one failure mode. `--history` mixes all modes; here mode 1 (`SharedDataMap: in shutdown`, 553x) started on 2026-09-09 while mode 2 (273x) was steady all window, and the combined per-day curve only hints at that.
- Got: per-day counts for all modes only; the failing-config table is also all modes (the worst config overall, windows11-ccov, has zero of mode 1).
- Workaround: `--task-ids --issue N --limit 0 --json` piped to a script counting `day` and `jobName`.
- What would have answered it: `--history --issue N` (per-day counts of that mode), and a per-config table restricted by `--issue N`.
