## The test's own TEST-* markers were not on the parent main thread

- Command: `profiler-cli thread markers --session S --category Test --search test_gfx_sanity_gpu --list --limit 0` on t-0 (parent GeckoMain), as the brief says.
- Expected: TEST-PASS / TEST-UNEXPECTED-FAIL markers.
- Got: only `SpecialPowers` markers in the Test category. For this mochitest-plain test the `TEST-*` markers are on the mochitest content process (`https://example.com (2/2)`).
- Workaround: `profiler-cli profile markers --search "TEST-UNEXPECTED,TEST-FAIL"`, which searches every thread.
- Suggestion: when a `--category Test` search on one thread finds nothing named `TEST-*`, say which threads do have them.

## A process whose threads are not in the profile goes unmentioned

- Question: which process hosted the page doing the work (the sanity check's `<browser>`)?
- Got: `profile info --all` lists 12 processes. The one that mattered (pid 3428, a preallocated content process) is not among them, and nothing says so.
- Workaround: `marker info` on the parent's `IPCIn PBrowser::Msg_AsyncMessage`, which gives `Other Pid: 3428`, then a search for `3428` found `Subprocess Priority` markers.
- Suggestion: list the child pids the parent's IPC or `Subprocess Priority` markers name but that have no threads in the profile, for example as "processes seen but not profiled".
