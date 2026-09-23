## Question: in which order, and how many ms apart, did these markers happen?

- Command: `profiler-cli thread markers --search "eventType:focus,eventType:blur,..." --list --limit 0` on a 24-minute profile.
- Expected: timestamps precise enough to order events a few ms apart (focus at +0 ms, blur at +65 ms, the INFO log at +60 ms).
- Got: `t=23m42s` for all of them; list rows are sorted but the gaps are invisible, and the Test INFO marker arriving via async IPC looks "before" the focus it logs.
- Workaround: `--json` and print `start/1000` with 4 decimals. A `--precise`/ms column (or relative-to-first-row deltas) in list mode would answer it.

## Question: which document/window did this DOMEvent / RefreshDriverTick / SetNeedStyleFlush belong to?

- Command: `profiler-cli thread markers --search "eventType:blur" --list`
- Expected: something distinguishing `blur - document` in the smart window chrome from `blur - document` in a tab document.
- Got: `blur - document` / `focus - window` with no window identity; the `innerWindowID` is only in `--json` data (or `marker info` "Other payload fields"), and never mapped to a URL.
- Workaround: `--json`, print `data.innerWindowID`, infer which ID is which window from TabOpen/TabSelect targets. Showing innerWindowID (and its URL when the profile has it in `pages`) in list rows would answer it.

## (review) Question: a link to a marker in a profile I opened from a report's link

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=N&thread=0>' --session X`, as the review brief says to, then `profile-link.py --session X --marker m-3`.
- Expected: a link for a new marker, built from the session that is already loaded.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`. I had to reload the ~0.6 GB profile from the raw URL just to get one link. profile-link.py could take the Taskcluster URL out of the `from-url` parameter.
- Also: loading the raw URL selected `t-28 (GeckoMain, Privileged Content)`, but loading the profiler link selected `t-0` (parent main thread). My first `--search` on the raw session returned 0 markers until I ran `thread select t-0`.
