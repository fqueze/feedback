## `zoom push` with the m/s time format the tool itself prints
- Command: `profiler-cli zoom push 23m20s,23m30s --session browser_console_eager_eval_resolve.js-1`
- Expected: zoom to 1400s-1410s (the tool displays times as `t=23m22s`), or an error.
- Got: success, with the view `ts-1→ts-2 (1.000ns)`; the following `thread markers` then matched nothing, which looks like "no markers there".
- Workaround: `zoom pop`, then use seconds or a marker handle.

## Question: "through which entry point was each of a manifest's 28 LSan-leaked objects allocated" (review-browser_console_eager_eval_resolve.js)
- Command: `profiler-cli marker stack m-6 --session review-eager_eval_resolve-1`, then the same for each of the other 27 `LSan Leak` markers.
- Expected: one call over a range or a search (`marker stack m-6..m-38`, or `thread markers --search "name:LSan Leak" --has-stack` with a stack column), since `marker info` already takes ranges.
- Got: `marker stack` takes a single handle, so 28 calls, then an awk script to find the caller of the innermost `js::RunScript` in each.
- Workaround: shell loop over handles into a file, then awk.
