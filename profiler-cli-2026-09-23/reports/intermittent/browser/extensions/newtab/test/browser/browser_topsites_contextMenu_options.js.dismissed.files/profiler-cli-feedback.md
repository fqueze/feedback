## Question: which document did these DOM changes happen in?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>` (zoomed on the 5 s wait), on a Privileged Content thread hosting both the test's about:newtab and the preloaded one.
- Expected: the list row to show the marker's `innerWindowID` (or the document URL / a short window handle), or a `--group-by field:innerWindowID` that works on it, so "did the test page change at all during the wait, or only the preloaded page?" is one command.
- Got: rows show only `SetNeedStyleFlush` and a time; `innerWindowID` is only in `marker info --json` under `rawFields`, not in `fields`.
- Workaround: a shell loop running `marker info <m> --json` per marker and printing `rawFields`, then `marker stack` per marker to tell the click's flush from React's.

## Question: which page did this JSActor message go to?

- Command: `profiler-cli marker info m-160 m-161 m-162 m-164` (parent `SendAsyncMessage` `[AboutNewTab] ActivityStream:MainToContent`).
- Expected: the target browser / innerWindowID of the actor, so a broadcast (to every newtab) can be told from a send to the preloaded page only.
- Got: only `Actor Name` and `Message Name`. (This may be a Gecko marker-payload limitation rather than the CLI's.)
- Workaround: counted sends per burst (2 = broadcast, 1 = preloaded only) and matched the content-side DOM changes by innerWindowID.
