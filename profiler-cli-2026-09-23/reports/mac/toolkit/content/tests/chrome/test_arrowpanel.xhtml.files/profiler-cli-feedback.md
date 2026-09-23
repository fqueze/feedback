## Question: which marker index is handle m-N (to check a link's `marker=N`)? (review-test_arrowpanel.xhtml)

- Command: `profiler-cli marker info m-1 --session <s>`
- Expected: the marker's index, since profile links carry `marker=<index>`.
- Got: type, time, fields, no index. It is only in `--json` (`markerIndex`). There, one handle returns an object and several return a list, so my first script over several handles broke.
- Workaround: `--json`, one handle per call.
