## Question: when did the samples in this function happen?

- Command: `profiler-cli thread functions --search createBackupOnIdleDispatch --include-idle --session ...` found 1 sample (f-4917), but no command gives its time.
- Expected: a way to list the sample times (or the time range) of samples matching a function / `--includes-function f-N`, e.g. `thread samples --includes-function f-4917 --list-times`.
- Got: call trees and totals only.
- Workaround: bisected with 11 `zoom push lo,mid` + `thread functions --search ...` + `zoom pop` rounds to find t≈33.425s.

## Question: does this profile cover the whole test, or did the buffer wrap?

- Command: `profiler-cli profile info` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/MFqr40K3TGCGNKedJf9MbA/runs/0/artifacts/public/test_info/profile_browser_settings.js.json
- Expected: a warning that the parent main thread's marker buffer wrapped (the file's first `Test` marker is at 5.5 s, mid-way through its 4th subtest; 7.0 M `DocAccessible::ContentRemovedNode` markers filled it).
- Got: nothing; only found out when `thread markers --category Test` started mid-file.
- Could have shown: in `profile info`, the first marker time per thread, or a "buffer wrapped / data starts at" note.
