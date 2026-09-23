## Question: which markers in a range have a stack through function X?
- Command: `profiler-cli thread markers --session S --has-stack --list --limit 0 --json`, then a shell loop running `profiler-cli marker stack m-N` on every handle and grepping for `setPageProxyState` / `getFixupPrimitives`.
- Expected: a filter on the stack, e.g. `thread markers --stack-search setPageProxyState`, listing the markers whose captured stack contains that frame.
- Got: `--search` matches only name, category, and payload fields, so I had to do ~100 `marker stack` round trips per range, six times over.
- Workaround: the loop above. Answering "did setURI change pageproxystate in this run?" came down to comparing `SetNeedStyleFlush` stacks, and this was the slowest part of the diagnosis.

## Question: in what order did these markers happen within the same millisecond?
- Command: `profiler-cli thread markers --search "NavigationFinished,WillChangeBrowserRemoteness,PBrowser::Msg_OnStateChange" --list`
- Expected: timestamps precise enough to order markers that are less than 1 ms apart.
- Got: `t=50.027s` for markers 28 us apart (NavigationFinished at 49676.232 ms, WillChangeBrowserRemoteness at 49676.260 ms). The race I was diagnosing is decided at that scale.
- Workaround: `--json` plus a python script to print `start` in ms. An option such as `--time-precision us`, or automatically adding digits when neighbouring rows share a value, would have avoided it.

## zoom push with an out-of-range value is accepted silently
- Command: `profiler-cli zoom push 53667.4,53669.0 --session S` (milliseconds typed by mistake; the profile is 55.5 s long)
- Expected: an error saying the range is outside the profile (0-55.533 s), or a hint that the values look like milliseconds.
- Got: success, and every later query returned an empty result, which I first read as "no such markers".

## (review) A session loaded from a report's profiler link cannot produce links
- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=594407&thread=0>' --session S` (as `review-brief.md` says to check a link), then `python3 profile-link.py --session S --marker m-4632`
- Expected: a link to the new marker, since the session knows the from-url artifact it came from.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself", for every marker. A reviewer who checks links then wants to add one must load the same 0.6 GB profile a second time.
- Workaround: reload from the raw Taskcluster URL. profile-link.py could extract the artifact URL from a `from-url/` link.
