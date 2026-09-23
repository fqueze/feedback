## Question: "how did the tests that started after time T end, in this job?"

- Command: `profiler-cli thread markers --session <s> --category Tasks --list --limit 0 --json | jq '.flatMarkers[] | select(.markerType=="Test" and .start > T) | .data.status' | sort | uniq -c`
- Needed to show that no test passed after the MSIX package broke (t=131.676 s in LKys8j8iTQKTAN9hKv040g). The default output has neither a start-time filter nor a group-by on a payload field for a marker list; `zoom push T,end` plus `--group-by field:status` restricted to `Test` markers would have answered it without jq.
- Workaround: the jq script above (`per-job.sh` in this directory).
- Correction after trying it: `zoom push 131.676,217` then `thread markers --search "type:Test" --group-by field:status` does work, but the zoom keeps every marker that overlaps the range, so it counted 13 PASS tests that started before the break and ended after it. What was missing is a "started within the range" option on the zoom or the marker list; the jq filter on `.start` is still needed.
