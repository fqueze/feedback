## Question: "which tests ran in this job, in order" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search 'asrouter/tests/browser/browser_' --list --limit 0`
- Expected: a way to get only the per-test `test` markers (`PASS — <path>` / `FAIL — <path>`).
- Got: those, interleaved with every `output`, `console.*` and `JavaScript error` marker whose payload names the path. That is several hundred lines for 33 tests. `--search name:test` is no help either: it matches `TEST-*` and more, because `name` is also a Text payload key.
- Workaround: pipe through `grep -E '^\s+m-[0-9]+\s+test\s'`. An exact marker-name filter (e.g. `--name test`) would answer it directly.

## Question: "which markers belong to window X" (review-browser_feature_callout_panel.js)

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4" --list`
- Expected: the markers whose `innerWindowID` is 4 (to tell whether a script loaded into the harness window or into a test's new window).
- Got: 0 markers. `innerWindowID` appears only under `rawFields` in `marker info --json`, and `--search` does not match it. I had to run `marker info <many handles> --json` and filter in Python.
- Also: `marker info --json` returns a bare object for one handle but `{markers: [...]}` for several, so the same script broke on one or the other.
- Also: `profiler-cli load <raw Taskcluster URL>` selected `t-27 (GeckoMain, Privileged Content)` rather than the parent main thread. The next query then ran silently on the wrong thread.
