# profiler-cli feedback (test_providerHeuristicFallback.js)

## `thread samples` counts the wrong samples after `zoom pop` / `zoom clear`

- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Sqxzf0nvQXmMR89NyaGWPg/runs/0/artifacts/public/test_info/profile_test_providerHeuristicFallback.js.json (GeckoMain has 737 samples per `thread info`).
- Sequence: `zoom push 10,20`, `zoom push 10,11`, `zoom pop`, `zoom push 12.0,12.002`, `zoom pop`, `zoom push 12.0,12.2`, `zoom push 3.348,3.668`, `zoom clear`, with `thread samples` / `thread markers` calls in between.
- Expected: `thread samples` at "View: Full profile" reports 737 samples; in `ts-L→ts-t` (10-20 s) 238.
- Got: after `zoom clear`, `thread samples --json` gave `categoryBreakdown.totalSamples: 7` (an earlier full-profile call gave 33); after the `zoom pop` back to 10-20 s it reported "20 running samples", while the same range freshly pushed reports 238. `status` said "Full profile / Filters: none" throughout.
- Cost: I read the difference (238 with `--include-idle`, 20 without) as "218 idle samples" and nearly built a CPU-starvation claim on it.
- Workaround: an explicit `zoom push 0,<end>` gives the right count (737, all non-idle), and so does each freshly pushed range.

## `--include-idle` output calls every sample "running"

- `thread samples --include-idle` prints "Categories (N running samples)" where N includes idle samples, so the header cannot tell "all running" from "idle included".

## (review) A bare `-term` in `thread markers --search` matches nothing instead of excluding

- Command: `profiler-cli thread markers --search "-DummyEvent" --list --limit 0` after `zoom push 3.340,3.670` on the Sqxzf0nvQXmMR89NyaGWPg per-test profile.
- Expected: every marker in the range except the `DummyEvent` runnables, or an error saying exclusion needs `-field:value`.
- Got: "0 markers (filtered from 38899)", "No markers match the specified filters."
- Workaround: `--search "-name:DummyEvent"`, after reading `marker info --json` for the field key.
