## (review) Which markers in a range were caused by a given JS function

- Question: which of the `SetNeedStyleFlush` markers during a test were caused by `set Element.className` in `computed.js!refresh`? I needed this to tell whether the `color` row changed class in an earlier refresh batch.
- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session …` (139 markers)
- Expected: a way to filter or group markers by their stack, e.g. `--stack-search className`, or the top stack frame shown in the `--list` row.
- Got: `--search` matches names and payloads, not stacks. The list shows only ✓/✗ for "has stack".
- Workaround: `--json` to collect the handles, then `marker info <139 handles> --json` and a Python script that prints the first frames of each. This took three commands and a script where one filter would have done.

## (review) A raw Taskcluster load selects a content-process thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Rzuu2YZ6QSecaQnY1i7JlQ/runs/0/artifacts/public/test_info/profile_browser_computed_inherited-element-backed-pseudo-elements.js.json --session …`
- Expected: the parent process GeckoMain, where the mochitest's `Test` markers are.
- Got: `t-26 (GeckoMain, Web Content (7/7))` was selected, so my next `thread markers --search` found nothing, with no hint as to why.
- Workaround: `thread list`, then `thread select t-0`.
