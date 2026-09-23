## Question: in what order, to the millisecond, did these markers run in a 2 s window?
- Command: `profiler-cli thread markers --session <s> --category Test --search test_findbar --list --limit 0` (the same happens with `marker info m-99 m-11`)
- Expected: timestamps precise enough to order markers that are 1 to 400 ms apart (e.g. `t=213950.7ms` or `3m33.951s`).
- Got: `t=3m34s` and `t=3m36s`: whole seconds, so the order of a timer, a TEST-PASS and a TEST-UNEXPECTED-FAIL within one second cannot be read.
- Workaround: `--list --json`, with a python one-liner printing `start` for each flatMarker. The text list could print milliseconds when the zoomed range is short, or always.

## (review) `--session` before the subcommand is rejected
- Command: `profiler-cli --session review-test_findbar.xhtml-1 zoom push 213.9,215.8`
- Expected: the session applied, as a global option.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand. Accepting it in either position, or naming the right position in the error, would save a retry.

## (review) Question: how long did each `Finder:UpdateHighlightAndMatchCount` query wait for its reply?
- Command: `profiler-cli thread markers --search Finder:UpdateHighlightAndMatchCount --list --limit 0 --session <s>`
- Expected: each SendQuery paired with its ReceiveQueryReply, with the wait between them.
- Got: two separate instant markers per query, with whole-second times.
- Workaround: `--json` and a script pairing each SendQuery with the next reply. The list could show a query and its reply as one interval with its duration.
