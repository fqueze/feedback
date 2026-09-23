## Per-config counts and task IDs of one failure mode that is split across rows

- Question: on which configs, and in which tasks, does the `Cannot show popup for an inactive window` failure of browser_ext_incognito_views.js happen?
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_incognito_views.js --task-ids --limit 0`
- Expected: that failure mode as one row with its count, its configs, and its task IDs (`--issue <n>`).
- Got: 169 `1x` rows, one per random `moz-extension://<uuid>` in the stack, so `--issue` gives one task at a time, and the Configs table mixes all modes.
- Workaround: `--json`, then a script filtering `taskIds[].message` on "inactive window" and counting by `jobName` and `day`.
- What the output could have shown: normalize `moz-extension://<uuid>/%7B<uuid>%7D` before grouping, and give each Issues row its own config breakdown.
