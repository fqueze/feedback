## Marker times past one minute lose their milliseconds in `thread markers --list`

- Command: `profiler-cli thread markers --search "tryOnce,waitForCondition,name:DevTools:RDP Front" --list --limit 0 --session browser_resources_reflows.js-4` on a profile where the test ran at t=66-68 s (https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ZlJ8kCXAROGf4SFzVi1rLA/runs/0/artifacts/public/test_info/profile_browser_resources_reflows.js.json).
- Question: in which order did two IPC packets and two `waitFor` polls happen? They are 1-90 ms apart.
- Expected: `t=67.581s`, as printed for markers under one minute (`t=47.931s`).
- Got: every row printed `t=1m7s` or `t=1m8s`, so the order within the second is unreadable; `zoom push 66.5,69` did not change the format.
- Workaround: `--json` and a Python one-liner printing `start/1000` rounded to 3 digits. The default output could keep millisecond precision past 60 s (`t=1m7.581s`, or plain `t=67.581s`).

## `profile markers --search` summary rows do not say which process a thread handle is

- Minor: `profile markers --search "name:DevTools:RDP Actor"` prints `t-57` / `t-56` per row and names the process only in the "Matches by thread" footer, which is cut when piping through `head`. Needed `thread list | rg` to map them. Not a big cost.

## `zoom clear` leaves `thread samples` on the old range (review of this report, profiler-cli 0.9.0)

- Commands, on the main thread of https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/fPhWZQumQCeAwrngokXr9w/runs/0/artifacts/public/test_info/profile_browser_resources_reflows.js.json: `zoom push 46.394,47.639`, `thread samples --include-idle` (23 running samples), `zoom clear`, `thread samples --include-idle`.
- Question: how many samples does the whole profile have, to check a report's "1,543 over the 78 s profile"?
- Expected: 1544, as after `zoom push 0,78` and `zoom pop`.
- Got: 23 again, under a header reading `View: Full profile`, and `status` also says `Full profile`. Repeated calls kept returning 23 until a `zoom push`/`zoom pop` pair. Reproduced in a freshly loaded session. It nearly made me call the report's figure wrong.
- Workaround: `zoom push 0,<end>` then `zoom pop` instead of `zoom clear`.
