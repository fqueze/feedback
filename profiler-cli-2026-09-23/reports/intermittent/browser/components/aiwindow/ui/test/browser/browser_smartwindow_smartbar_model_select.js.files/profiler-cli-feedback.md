## Question: "in what order, to the millisecond, did these focus/key/click events happen?" (long per-test profile)

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` on a 17m49s profile (fmuR0OnERJasEwNL1G81HQ, profile_browser_smartwindow_smartbar_model_select.js.json), zoomed to 1.5 s.
- Expected: start times with millisecond precision (e.g. `t=17m10.094s`), since the zoom is sub-second and the question is ordering within ~200 ms.
- Got: every row printed `t=17m10s`; the same for `marker info` (`Time: 17m9s (instant)`) and for the Test-category log. The order of focus/blur/keydown/click within the second could not be read, and a few rows (the longer ones) were not even in start order.
- Workaround: `--list --json` and a python script sorting `flatMarkers` by `start` and printing `%.3f`. Needed in every profile I read. The output could have shown ms precision whenever the zoom range is under a few seconds, or always for times past 1 minute.

## Question: "what is the end time of this interval marker?" (SpecialPowers Spawn / ContentTaskUtils waitForCondition)

- Command: `profiler-cli thread markers --category Test --list --limit 0 --json`, reading `end` from `flatMarkers`.
- Expected: `end` for interval markers.
- Got: no `end` key in `flatMarkers` rows (if it is there under another name, I did not find it), so I called `marker info m-A m-B ... --json` to get `start`/`end`.
- Workaround: `marker info` on each handle. The flat list could carry `end`/`duration` like `marker info` does.

## Question: "is this profile loaded yet?" (review-browser_smartwindow_smartbar_model_select.js)

- Command: `profiler-cli load --session review-model_select-A <profiler.firefox.com from-url link to a 17m49s per-test profile>`
- Expected: the command waits until the profile is loaded, or says it keeps loading in the background.
- Got: `Error: Profile load timeout after 60000ms` and a non-zero exit, while the daemon went on loading; `status` then answered `Profile still loading, try again shortly` for a few minutes, then worked.
- Workaround: polled `status` in a background loop; `PROFILER_CLI_LOAD_TIMEOUT_MS=600000` for the next loads. The error could say the load continues and how to wait for it, or `load` could wait longer by default for remote profiles.
