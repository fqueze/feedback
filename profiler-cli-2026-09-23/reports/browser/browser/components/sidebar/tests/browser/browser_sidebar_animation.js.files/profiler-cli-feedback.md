## Question: "in what order, at ms resolution, did these markers happen?" (past the first minute)

- Command: `profiler-cli thread markers --session S --search "Preference Write,DeferredTask,sidebar.expand" --list --limit 0`
- Expected: start times precise enough to order events that are 2-30 ms apart (e.g. `t=87.2952s`).
- Got: every marker after 60 s shows as `t=1m27s` / `t=1m28s`, so a pref write, the DeferredTask arm 2.6 ms later and the next pref write 1 ms after that all read the same. `marker info` also prints `Time: 1m27s`.
- Workaround: `--json | jq '.flatMarkers[] | "\(.start) \(.name) \(.label)"'`. The list could print ms (`t=87295.9ms` or `1m27.296s`) once the profile is longer than a minute.

## `zoom push` with millisecond values silently zooms outside the profile

- Command: `profiler-cli zoom push 87400,88450 --session S` on a 90 s profile (I passed ms, it takes seconds).
- Expected: an error ("range 87400s-88450s is outside the profile, 0-89.9s; values are seconds").
- Got: accepted, view became `(17m30s)`, and `thread markers --has-stack` then said "No markers match the specified filters" — reads like an empty range rather than a wrong unit.
- Workaround: `zoom push 87.4,88.45`.

## Question: "which of these markers have function X on their stack?"

- Needed: the SetNeedStyleFlush markers whose stack includes `set launcherExpanded` (to see which window's launcher got expanded, and by whom).
- Command: no direct way; I looped `profiler-cli marker stack $h` over 379 handles from `thread markers --search name:SetNeedStyleFlush --list --json` and grepped.
- Could have shown: a `--stack-contains <text>` filter on `thread markers` (or a `stack:` search field), returning the 4 matches directly.

## (review) `marker stack` takes one handle, unlike `marker info`

- Question: same as above, "which of these SetNeedStyleFlush markers have `set launcherExpanded` on their stack?", over 3 ranges (335, 198, 166 markers).
- Command: `profiler-cli marker stack m-1557..m-1891 --session S`
- Expected: the stacks of the whole range, the way `marker info` accepts `m-A..m-B` and several handles.
- Got: one handle per call. Workaround: a shell loop, about 700 calls.
