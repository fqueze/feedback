## Which document's refresh driver keeps vsync on?

- Command: `profiler-cli thread markers --session <s> --search "name:RefreshDriverTick waiting for paint" --list --limit 0 --json | python3 -c '<group by data.innerWindowID, first/last start>'`
- Expected: a way to see, without a script, which window(s) the 19,425 `RefreshDriverTick waiting for paint` markers belong to, and over what span, e.g. `--group-by field:innerWindowID` working on raw payload fields (these markers have no schema, so the field is only in `rawFields`/`data`), and the innerWindowID resolved to its page URL.
- Got: the aggregate view only gives name counts and frequency; the innerWindowID shows only in `marker info`, as a bare number (4294967338). I had to match it by hand against `DocumentLoad` markers' `innerWindowID` to learn it was `moz-extension://.../popup.html`.
- Workaround: the script above, plus `marker info` on each `DocumentLoad`.

## Sandbox: default session dir not writable

- Command: `profiler-cli load <url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked (`/Users/florian/.sandbox/tmp/profiler-cli`). Cheap, but every agent in this sandbox hits it once.

## (review) Which process does the parent send vsync to during a wait, and until when?

- Command: `profiler-cli thread markers --session <s> --search PVsync --group-by "field:otherPid,field:messageType"` after `zoom push <wait marker>`, then `--list --limit 0 --json | python3 -c '<first/last start per otherPid>'`.
- Expected: the group-by view to give each group's first and last time, since "until when" is the question once counts show one process dominating.
- Got: counts and durations per group only; needed a script over `flatMarkers` to learn that everything but pid 2616 stopped in the wait's first 250 ms.
- Also: `--search innerWindowID:4294967338` matches 0 markers on these no-schema markers, while `--group-by field:innerWindowID` does group them by it (contrary to the entry above, grouping works in this version). Search and group-by disagree on which fields exist.
