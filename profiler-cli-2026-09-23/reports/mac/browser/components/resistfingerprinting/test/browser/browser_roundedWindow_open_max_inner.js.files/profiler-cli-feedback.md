# profiler-cli feedback — browser_roundedWindow_open_max_inner.js

## Question: in which order did these markers happen, within the same second, in a profile longer than a minute?

- Command: `profiler-cli thread markers --session <s> --search UpdateDimensions,SpecialPowers --list --limit 0`
  (also `profiler-cli marker info m-6 m-9 m-16 m-11`)
- Expected: start times precise enough to order markers a few ms apart (the race here is a
  reply at 168.8512s against an IPC send at 168.8538s).
- Got: every row printed as `t=2m48s` or `t=2m49s`; `marker info` prints `Time: 2m49s`. The
  order of the rows is chronological, but the gap between two markers (is it 2 ms or 900 ms?)
  cannot be read, and it is the whole question in a race.
- Workaround: `--json` and a script printing `start/1000` with 4 decimals
  (`ml.py` in this directory). Once past one minute, the text output could print
  `t=2m48.4549s` or seconds with ms, as it does for profiles under a minute.

## Question: which process is pid N?

- Command: `profiler-cli profile info --session <s> --search 4339`
- Expected: either the process, or "no process or thread matches 4339".
- Got: the header and `(CPU time information not available)`, with no statement that nothing
  matched. The pid was the other end of IPC markers but its process is not in the profile at
  all (the live content processes of the failing test were not captured), which I only
  established by dumping `thread list` to a file and grepping it.
- Workaround: `thread list > file; rg <pid> file`.

## First command after load timed out

- Command: `profiler-cli profile info --session <s>` right after `load` returned (122 MB
  compressed profile).
- Got: `Error: Timed out after 30000ms waiting for the daemon ... to answer.` The same command
  succeeded when re-run.

## Screenshots: "captured without the screenshots feature" contradicts `profile meta`

- Command: `profiler-cli screenshots --session <s> --at 168.845 -o <dir>` on a CI per-test profile.
- Got: `No screenshots at t=2m49s. The profile has 0 screenshot marker(s). This profile was
  captured without the "screenshots" feature.` — while `profiler-cli profile meta` on the same
  session lists `Features: js, screenshots, ipcmessages, processcpu, memory`.
- Expected: a message that does not contradict `meta`, e.g. "the screenshots feature was on
  but no CompositorScreenshot marker was recorded".

## (review) Question: when was this IPC message sent?

- Command: `profiler-cli marker info m-51 --session <s> --json` on an `IPCOut PBrowser::Msg_UpdateDimensions`
  marker (CaZcwM7n per-test profile).
- Got: `"start": 168456.212333` next to `rawFields` `startTime: 168464.691083`,
  `sendStartTime: 168464.692833`. That is about 8.5 ms apart, on every IPC marker of the thread.
  The text output shows neither.
- Expected: one send time, or a note saying which clock each field uses. In a race decided by
  a few ms, I could not tell which time to put next to the other markers. `start` agreed with
  the neighbouring `Reflow` marker, and I ordered the messages by `messageSeqno` instead.
