## Verdict line reads as self-contradictory

- Command: `fx-tests test netwerk/test/unit/test_cookies_purge_counting_per_host.js`
- Expected: "Fails almost only in parallel" backed by a higher parallel failure rate.
- Got: `Fails almost only in parallel: 47 of 48 mode-recorded failures, 0.4% of parallel runs against 3.2% of sequential ones`. The sequential rate is 8x the parallel one, so the sentence argues against its own headline. (The sequential population is presumably mostly harness reruns of failures, which biases it.)
- Workaround: ignored the parallel/sequential claim; the mac retry profile showed the failure reproduces when run sequentially.
- Suggestion: say what the sequential population is (reruns of failed tests?) or drop the rate comparison when it is that biased.

## `--issue 1 --task-ids` lists one job twice

- Command: `fx-tests test netwerk/test/unit/test_cookies_purge_counting_per_host.js --task-ids --limit 0 --issue 1`
- Expected: 27 jobs, each once, in date order.
- Got: `Task IDs (27 jobs, ...)`, then `FN5woBMaT-ibCeu1feHsyQ.0` listed under 2026-09-18 and again in a trailing `2026-09-18` group after 2026-09-21 (it failed in both the first run and the retry). The same duplicate appears without `--issue`.
- Workaround: counted by hand; 28 runs across 27 jobs.
- Suggestion: one row per job, with an "x2 (retry failed too)" marker.
