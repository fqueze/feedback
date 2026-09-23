# fx-tests feedback (bug 1998232)

## `fx-tests task` does not list the leakcheck failure that turned the job orange

- Command: `fx-tests task Lb9XmJQYQ0SLO0zJyoYhYA --profiles`
- Expected: the job's failures, including `TEST-UNEXPECTED-FAIL | leakcheck | default 2800 bytes leaked (...)` for scope `gfx/layers/apz/test/mochitest/mochitest.toml`, which is what the sheriffs starred on this bug.
- Got: "1 failing" and only `test_group_overscroll_handoff.html` (which passed on the harness rerun). No sign of the leakcheck failure, nor of which manifest scope it was in.
- Workaround: downloaded `public/logs/live_backing.log` and grepped `for scope` / `leakcheck`.

## Question: "which passing tasks ran this test on this config?"

- To compare a leaking run against non-leaking runs of the same manifest on the same config, I needed task IDs of *passing* `test-macosx1500-aarch64/debug-mochitest-plain*` jobs that ran `gfx/layers/apz/test/mochitest/mochitest.toml`.
- `fx-tests test <path> --task-ids` gives only the failing tasks. There is no option to list passing ones.
- Workaround: Treeherder `api/jobs/?push_id=...` over about 120 pushes, downloading every mac debug mochitest-plain log and keeping the ones whose log named the manifest scope. That cost about 90 log downloads.
- What would have answered it: `fx-tests test <path> --task-ids --outcome pass --config <substr>`, or a per-config sample of passing task IDs.
