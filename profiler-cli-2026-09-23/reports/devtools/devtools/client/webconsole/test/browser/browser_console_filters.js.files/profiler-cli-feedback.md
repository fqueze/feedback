## Question: what did the parent main thread log between two absolute timestamps taken from `marker info --json`?

- Command: `profiler-cli zoom push 1402000,1402400 --session review-browser_console_filters.js-1` (values from `marker info --json` `start`, which is in absolute ms), then `thread markers --search Shutdown --list`
- Expected: either the range interpreted like the `start` field it came from, or an error, since 1402000 s is far past the profile's 24m55s end.
- Got: accepted silently as a 6m40s range entirely outside the profile (`ts>10u→ts>10U`), and every later query returned "0 markers in view".
- Workaround: converted to seconds from profile start by hand (`zoom push 1385,1402`). A warning for a range outside the profile, or `marker info --json` also giving the start relative to the profile, would have avoided it.
