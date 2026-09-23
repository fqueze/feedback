## Question: which of two markers printed at the same millisecond came first, and by how much?

- Command: `profiler-cli profile markers --search a_counter` and `thread markers --category Test --list` (session loaded from `XVNLSBsOShippQlTaDZJbQ/.../profile_browser_fog_gpu.js.json`)
- Expected: enough precision in `t=` to order the parent's `Counter::add test_only.ipc.a_counter : 5` against the `TEST-UNEXPECTED-FAIL` that follows it.
- Got: both print `t=2.943s`; the list view rounds instants to 1 ms.
- Workaround: `profiler-cli marker info m-491 m-40 --json | python3 ...` to read `start` (2942.728 vs 2943.173 ms).
- What the output could have shown: sub-millisecond `t=` for instant markers in `--list` (as durations already are), or at least in `marker info` text output.

## `--search name:test` is a substring match, so it cannot select the harness's `test` markers

- Command: `profiler-cli thread markers --list --limit 0 --search "name:test,TEST-UNEXPECTED-FAIL,..." --session browser_fog_gpu.js-2`
- Expected: the per-test `test` markers (`PASS — <path>`), as `name:` narrows to the name field.
- Got: every marker whose name contains "test" (`SanityTest.sys.mjs` imports, `notifyCompleteOnCallingThread` runnables...), 446 rows before the first useful one.
- Workaround: add `--category Test`.
- Suggestion: an exact-match form (`name:=test`) or a note in `thread markers --help` that `field:value` is a substring match.

## Question (review): which marker does this link's `marker=N` point to?

- Command: `profiler-cli marker info m-12 m-16 m-17 ... --session review-browser_fog_gpu.js-1` to check 26 report links against their quoted markers.
- Expected: the marker index shown, so a link's `marker=N` can be matched to an `m-` handle.
- Got: the text output has no `markerIndex`, so each check needs `--json`. `--json` records also have no `handle` field, so a multi-handle call needs its handles zipped back by position.
- Workaround: `marker info <handles> --json | python3 -c ...` printing `markerIndex`, `name`, `start`.
- What the output could have shown: `Index: N` in `marker info` text output, or a way to look a marker up by index (`marker info --index 72079`), which would check a link in one command.
