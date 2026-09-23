## Question: in what order did these markers happen, to the millisecond? (`--list` prints `t=4m5s`)

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0` (and the same with `--search "SendQuery,ReceiveQueryReply,..."`, and `profile markers --search addOrSetSessionDataEntry`)
- Expected: a start time precise enough to order events that are milliseconds apart. The race here is a content-process reply arriving 0.2 ms after the test's last check, inside one second of profile time.
- Got: every row shows `t=4m5s` (or `t=7m6s`); `marker info` shows `Time: 4m5s - 4m5s (29.808ms)`. The order within that second is only the list order, and for `profile markers` across threads I could not tell whether it was sorted by time.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 5 decimals, sorted; I needed it about ten times.
- What the output could show: `t=` with ms precision (e.g. `t=244.7917s`) once the profile is longer than a few seconds, or relative to the current zoom start.

## IPC marker payload times are on a different clock from the marker timeline, silently

- Command: `profiler-cli marker info m-134` (IPCIn), and `thread markers --search "otherPid:2050" --list --json` reading `data.sendStartTime / recvEndTime / endTime`
- Expected: `startTime`, `sendStartTime`, `recvEndTime`, `endTime` in the same time base as `t=` and the marker `start`.
- Got: they are offset by ~7.7 ms (task FN0yyKEeT86FLshnIo3tyg), ~10 ms (TGLF3XP9Ql2x3jcadUlJCA), ~8.06 ms (aFAqCuyGRveSmvLsS6yoIg -2) from the marker's own `start`. I first concluded a content process's clock was skewed, and that the parent sent a JSActor query 8 ms after its `SendQuery` marker, before noticing every IPC marker had the same offset.
- Workaround: use the marker `start` and the printed duration; ignore the payload's absolute times.
- What the output could show: the payload times converted to the profile timeline, or a note that they are raw.

## Default session directory not writable under the sandbox

- Command: `profiler-cli load <url> --session <id>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did name the fix (`PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`). One wasted call; the brief does not mention it.


## (review) Session name too long for the socket path; the suggested fix was the directory already in use

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <url> --session review-browser_toolbox_rulers_button_highlighter_reload.js-1` (the `review-<report name>-N` form the review brief asks for)
- Expected: the session loads, or an error naming the session id as the part to shorten.
- Got: `The Unix socket path for this session is 106 bytes, over this platform's 103-byte limit`, suggesting `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` — the directory already set.
- Workaround: a shorter session id (`review-rulers_hl_reload-N`), keeping the long name only in `PROFILER_CLI_SESSION_OWNER`.
- What the output could show: that the session id is the long part, with its length; or hash long ids into the socket file name.
