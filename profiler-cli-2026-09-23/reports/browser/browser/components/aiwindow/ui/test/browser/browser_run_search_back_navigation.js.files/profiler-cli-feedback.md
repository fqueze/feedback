## Question: in what order did these markers happen, to the millisecond?

- Command: `profiler-cli thread markers --session S --category Test --search browser_run_search_back_navigation --list --limit 0` (and every other `--list`) on a 25-minute profile.
- Expected: start times precise enough to order events (ms), e.g. `t=1510.323s`.
- Got: `t=25m10s` for every marker in the 3.5 s subtest, so a channel Suspend, a CC slice and the SERP load all read `25m10s`/`25m11s`.
- Workaround: `--json` and a script printing `start`/`duration`. The list could show seconds with ms once the profile is longer than a minute, or at least inside a zoom shorter than a few seconds.

## Question: which IPC message is this, and between which processes?

- Command: `profiler-cli thread markers --session S --category IPC --list --limit 0`
- Expected: the message type and the other process in the row (`PBrowser::Msg_OnStateChange from https://example.com (18/18)`).
- Got: every row is just `IPCIn` / `IPCOut`, with no label.
- Workaround: `--json` and reading `data.messageType`, `data.sendThreadName`, `data.sendStartTime`. `--search messageType:OnStateChange` works, but the output still does not say which process sent each one or when it was sent.

## Question: when did each content process start and end?

- Command: `profiler-cli profile info --session S --all --json`
- Expected: per-process `startTime` / `endTime` in the profile's time base.
- Got: `startTime` is ~1047.38 for all 34 processes (including ones the text output shows starting at different `ts-` handles), while `endTime` looks right.
- Workaround: ignored the start times; used the process's own markers instead.

## Review: `--session` before the subcommand is rejected (browser-review, 2026-09-22)

- Command: `profiler-cli --session S zoom push 1510.0,1513.5`
- Expected: `--session` accepted as a global option, as the brief's "pass `--session` on every call" suggests.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session S` after the subcommand's own arguments.

## Review: a reload of a profile loaded minutes earlier times out (browser-review, 2026-09-22)

- Command: `profiler-cli load <bpjZ per-test URL> --session S` (the same URL had loaded fine 40 minutes earlier)
- Expected: the load to finish, or to wait as long as it takes.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, then `Profile still loading` from every command; the daemon kept loading in the background.
- Workaround: poll `profiler-cli status --session S` until it answers.
