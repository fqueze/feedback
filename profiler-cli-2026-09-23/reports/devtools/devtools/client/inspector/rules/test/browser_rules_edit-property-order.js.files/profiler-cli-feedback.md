## Marker list times too coarse to order a test's own log

- Question: at what time exactly did each step of the test happen (to zoom right after the last INFO line)?
- Command: `profiler-cli thread markers --category Test --search browser_rules_edit-property-order --list --limit 0 --session <s>`
- Expected: timestamps with ms precision (e.g. `207.060s`), since a whole mochitest often runs within one second.
- Got: every marker of the test printed as `t=3m27s`; `marker info` also prints `Time: 3m27s`. Only the timeout shows a different value.
- Workaround: the same command with `--json` and reading `.flatMarkers[].start`.

## Question: which code caused each of the ~70 SetNeedStyleFlush markers in a 20 ms window

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --json`, then one `profiler-cli marker stack m-N` per handle in a shell loop (about 70 calls).
- Expected: a way to print each listed marker with the top few frames of its stack (e.g. `--list --with-stack 6`), since `--has-stack` markers are exactly the ones where the stack is the information.
- Got: the list shows only `SetNeedStyleFlush` for every row; the cause is only reachable one marker at a time.
- What it could have shown: one line per marker, `t  name  frame1 < frame2 < ...`, which is what I built by hand and what the diagnosis rested on.


## review-browser_rules_edit-property-order.js (2026-09-22)

- Command: `profiler-cli --session review-epo-A zoom push 207.035,207.065`
- Expected: `--session` accepted as a global option before the subcommand, as with most CLIs.
- Got: `error: unknown option '--session'`; it only works after the subcommand. The guide's examples never show where `--session` goes.
- Workaround: put `--session` last.

- Command: `python3 $D/profile-link.py --session review-epo-A --marker m-413`, on a session loaded with `profiler-cli load <report link>` as `review-brief.md` instructs.
- Expected: a link for an extra marker found while reviewing.
- Got: refused: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link." The review brief says to load the report's link; the link script then cannot be used from that session.
- Workaround: cited the marker by time and stack in the review instead of linking it.
