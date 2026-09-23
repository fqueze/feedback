## Question: "in which order, to the millisecond, were these log lines emitted?"

- Command: `profiler-cli thread markers --session <s> --search test_saveHeapSnapshot_e10s_01 --list --limit 0` and `profiler-cli marker info m-56 m-58 m-59 m-60`
- Expected: marker times precise enough to order log lines that are a few ms apart (e.g. `t=5m50.496s`), at least in `marker info`, and ideally a wall-clock (epoch) time, since the log lines carry epoch ms in their payload.
- Got: `t=5m50s` in the list and `Time: 5m50s (instant)` in `marker info`, so a dozen markers inside one second are indistinguishable. Comparing with the epoch `time` inside a raw log line also needs the profile start, which is only in `profile meta` at ms precision.
- Workaround: `--json` piped through a python script to read `start`, plus `profile meta`'s `Started:` to convert to epoch ms.
## Question: "at what epoch ms was this marker stamped?" (review-test_saveHeapSnapshot_e10s_01.js)

- Command: `profiler-cli profile meta --session <s>` (text), then marker `start` + `Started:`.
- Expected: a profile start precise enough to turn marker starts back into the epoch ms carried in log-line payloads, or the epoch time shown directly in `marker info`.
- Got: `Started: 2026-09-18T00:24:08.904Z`, truncated, while `profile meta --json` has `"startTime": 1789691048904.755`. Converting with the text value gave every epoch 1 ms low. The original report's ms-level ordering was wrong because of it (a "2 ms" gap that is really 1 ms).
- Workaround: `profile meta --json` `startTime` + marker `start`.
- Suggestion: print `startTime` with its sub-ms part in the text output, and show each marker's epoch time in `marker info`.
