## `marker info` on Network markers: payload times on a different zero than `t=`

- Command: `profiler-cli marker info m-66 --session browser_net_status-bar.js-1` (Network marker, task EiEdlc3oRV-1r25RtS9l_Q profile_browser_net_status-bar.js.json)
- Expected: `startTime`, `endTime`, `requestStart`, `responseStart`… on the same time base as the list (`thread markers --list --json` start 139047.942, duration 7.177ms).
- Got: `startTime: 139055.1255`, `endTime: 139062.3024` — every payload time is +7.184ms, which is `profile meta`'s "Uptime" (meta.profilingStartTime). It made the channel look like it started after its own `nsHttpChannel::OnStopRequest` marker, and I lost time on a false causality contradiction.
- Workaround: subtract the "Uptime" from every payload time field.

## `zoom push` with milliseconds accepted silently

- Command: `profiler-cli zoom push 139030,139200 --session ...` (meant ms, the unit `--json` `start` fields use)
- Expected: an error, since the range is far past the 2m20s profile.
- Got: "View: ts>10X→ts>10y (2m50s)", then empty results from later commands.
- Workaround: seconds (`139.030,139.200`).

## `profile meta` "Started" is startTime + profilingStartTime, not meta.startTime

- Question: what epoch is `t=0`, to compare profile times with a `Date.now()` value from the log / an artifact name.
- Command: `profiler-cli profile meta`. "Started: 2026-08-31T06:45:45.104Z" is meta.startTime (…097.178) + 7.184ms, only to the millisecond, and not labelled as the zero of `t=`.
- Workaround: downloaded the profile and read `meta.startTime`/`profilingStartTime` with python.
