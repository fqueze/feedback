## Question: which tests ran in this browser before mine, with their status

- Command tried: `profiler-cli thread markers --category Test --search "type:Test" --list --limit 0` — matched every Test-category marker (thousands of TEST-PASS), not the per-test `test` markers.
- Expected: a way to list only the markers named `test` (one per test file, with PASS/FAIL).
- Workaround: `--search "finished in"`, which happens to match the `message` field of those markers only.

## Question: at what time did a sample with a given frame occur

- Wanted: the time of the one sample where `ASRouter.sendTriggerMessage` ran under `sinon-7.2.7.js!proxy` in an 82 s range.
- Command: `thread samples-bottom-up --search sendTriggerMessage` found it, but gives no timestamp; I had to bisect with `zoom push`/`zoom pop` over 13 invocations to narrow it to 200 ms.
- Expected: sample timestamps (or first/last matching time) in `--search` output, or a `thread samples --list`.
