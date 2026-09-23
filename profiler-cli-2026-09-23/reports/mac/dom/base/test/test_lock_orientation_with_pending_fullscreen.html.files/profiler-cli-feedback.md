## Test-category `INFO` Log markers print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on the content GeckoMain of a mochitest-plain per-test profile (EQ-0yXdzSTqoMkLa-1bQCA, `profile_test_lock_orientation_with_pending_fullscreen.html.json`), and `profiler-cli marker info m-11`.
- Expected: `INFO  add_task | Entering test_pending_fullscreen_request`.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info --json` shows `"value": "add_task | Entering test_pending_fullscreen_request"` with `"formattedValue": "(empty)"` for both fields: the formatter drops the value.
- Workaround: `marker info <handles> --json` and read `fields[].value`.
- Cost: the test's own log (which add_task is running when) cannot be read from the default output.

## Reading the test's log needed a script over `marker info --json`

- Question: in which add_task was each assertion made? The `INFO add_task | Entering …` lines give that.
- Command: `profiler-cli marker info m-11..m-22 --json`, then a Python loop over `fields[].value`.
- The default `thread markers --category Test --list` output could have shown it if it printed the Log markers' `message` value (see the "(empty)" entry above).

## (review) Checking a link's `marker=N` needs a script over `marker info --json`

- Question: which marker does a report link's `marker=N` point at? Checking one link means matching N to a handle.
- Command: `profiler-cli marker info m-45 m-46 … --json --session <s>`, then a Python loop printing `markerIndex`, `name`, `start` and `fields[].value` (0.9.0).
- The default `marker info` output shows no marker index, and nothing takes an index as input. Printing `Index: N` in `marker info` would answer it. So would a `thread markers --list` column, or `marker info --index N` returning the handle.
- Cost: one scripted call per thread in each of 3 profiles.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-lockorient-A thread markers …`
- Expected: the session selected, as for any global option.
- Got: `error: unknown option '--session' (Did you mean --version?)`. It works only after the subcommand. The suggestion points away from the fix.
- Workaround: put `--session <id>` at the end.
