## Issues list splits one failure mode per moz-extension UUID

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_incognito_views.js`
- Expected: the "Cannot show popup for an inactive window" failure listed once with its count.
- Got: 169 separate `1x` rows (issues 3 to 171), each differing only by the random `moz-extension://<uuid>/%7B<uuid>%7D.js` in the stack, with "↑ same as 3, but" noting it. The failure mode looks like a 1-off when it is the second most frequent (169 of 570), and the list is 900 lines long.
- Workaround: count the rows by hand from `--limit 0` output.
- Suggestion: normalize `moz-extension://<uuid>` (and `%7B<uuid>%7D`) before grouping, as already done for numbers like `<n>ms`.

## The task IDs of one failure mode, when it is split across rows

- Question: the failing tasks of the "Cannot show popup for an inactive window" failure mode.
- Command: `fx-tests test <path> --task-ids --issue 3 --limit 0`
- Expected: all ~169 tasks of that failure mode.
- Got: the single task of row 3; each of rows 3 to 171 has to be asked for separately (`--issue 150`, `--issue 171`, ...).
- Workaround: sampled a few row numbers to get recent tasks.
