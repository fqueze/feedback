## Question: in what order, to the millisecond, did these markers happen within one subtest?

- Command: `profiler-cli thread markers --search "DOMEvent,Test,L10n,..." --list --limit 0 --session tgcm-1` after `zoom push 206.735,206.83` (a 95 ms zoom, 3m27s into the profile).
- Expected: each row's time precise enough to order markers within the zoom (ms, or relative to the zoom start).
- Got: every one of the 342 rows reads `t=3m27s`; the time column is rounded to the second once past one minute, so the list's order is the only timing left, and durations/gaps between an event and a test assertion cannot be read.
- Workaround: `--json` and a script printing `start` in ms.

## Question: for each opening of a popup, did a refresh driver tick happen between its popupshowing and its popupshown?

- Command: `profiler-cli thread markers --search "eventType:popupshowing,eventType:popupshown,eventType:popuphiding,RefreshDriverTick" --list --limit 0 --json`, then a Python script pairing each `popupshowing - menupopup id="tabContextMenu"` with the next `popupshown` of the same popup and counting `RefreshDriverTick` rows in between.
- What the output could have shown: a way to pair start/end markers that are separate instants (popupshowing/popupshown, Entering/Leaving test) and count or list another marker between each pair — the list gave the rows, but pairing 28 openings by eye across a 5-second list was not practical.
- Workaround: the script (popupseq.py in this directory).

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Expected: a session. Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did say to set `PROFILER_CLI_SESSION_DIR`, which worked (`/Users/florian/.sandbox/tmp/profiler-cli`). Cost one round trip; the brief's command templates do not mention it.

## Reviewer (review-browser_tab_groups_tabContextMenu.js): hit the same two issues again

- The `t=3m27s` time column (first entry above) and the default session directory (third entry) each cost me a round trip again. That is a second agent hitting both.

## Question: can I build a link from a session loaded from a profiler.firefox.com link?

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/<encoded TC URL>/marker-table/?marker=…&thread=0&v=17' --session …`, as the review brief says to, then `profile-link.py --session … --marker m-90`.
- Expected: a link. Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`. The session records the profiler URL as given, and not the `from-url` it decoded.
- Workaround: loading the Taskcluster URL again (another 0.6 GB load). That session then opened on `t-351 (Privileged Content)`, not on the parent main thread, which cost one more round trip.
- What would help: the session keeps the decoded artifact URL, so that either form of load can produce links.
