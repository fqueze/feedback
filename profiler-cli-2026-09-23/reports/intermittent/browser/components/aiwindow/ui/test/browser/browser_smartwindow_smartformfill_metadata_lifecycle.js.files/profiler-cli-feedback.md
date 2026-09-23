## Bare term in a comma list matched unrelated INFO markers

- Command: `profiler-cli thread markers --session <s> --search 'out of memory,CRASH,name:FAIL' --list --limit 0` on a resource-usage profile (bhX-PGS0TyGJ0HZXJA0H3w).
- Expected: the OOM, CRASH and FAIL markers.
- Got: first ~35 rows were `INFO ==> process 2292 launched child process ... -contentproc ...` and `INFO crashreporter: True`, none of which contain "out of memory" or a FAIL name, pushing the real hits out of a `head`. Probably a case-insensitive substring match of `CRASH` against some payload field (crashreporter, a hidden field of the launch lines).
- Workaround: `zoom push` onto the test's marker first, or `name:CRASH`. Showing which field matched, or a `--case-sensitive` option, would have avoided the guess.


## profile-link.py refuses a session loaded from a profiler.firefox.com link (review)

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…EKBRyA…/marker-table/?marker=53888&thread=0&v=17' --session <s>` (as review-brief.md says to check a link), then `profile-link.py --session <s> --marker m-873`.
- Expected: a link to the new marker, since the session knows the underlying artifact URL (it is inside the from-url link).
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.` and no link.
- Workaround: stop, and reload the same 0.6 GB profile from the raw Taskcluster URL just to link a new observation. profile-link.py could unwrap `from-url/<encoded>` itself.

## The marker at a link's `marker=N`, among many matching ones (review)

- Question: which of 432 `parseXULToFragment` console.error markers is the one a report links as `marker=42819`?
- Command: `profiler-cli thread markers --session <s> --search parseXULToFragment --list --limit 0` (JmMoctU4QxCo0G4t3_az2A resource-usage).
- Expected: a way to go from the link's index to a handle, e.g. `marker info --index 42819`, or the index in the `--list` rows.
- Got: handles only; `--json` flatMarkers has no markerIndex either, so `marker info` on each of 432 handles is the only way.
- Workaround: none tried; checked only that such markers exist at the quoted time.
