## Question: which IPC messages did this thread exchange with process X, in order?

- Command: `profiler-cli thread markers --search otherPid:5640 --list --limit 0 --session browser-browser_eme.js-1`
- Expected: one row per IPC marker with its message type (e.g. `PUtilityMediaService::Msg_GetKeySystemCapabilities`), direction and other pid.
- Got: rows reading only `IPCOut` / `IPCIn` with a time and duration; the message type, the one thing that tells IPC markers apart, is missing from the list.
- Workaround: `--json` piped into a Python script printing `data.messageType`, `data.direction`, `data.otherPid`. Needed on three threads in two profiles.

## Question: when was this process/thread created?

- Command: `profiler-cli thread info --session browser-browser_eme.js-1` (thread t-20), and `profile info --all`
- Expected: `Created at: 9.300s`.
- Got: `Created at: ts-o7`, and process lifetimes as `[ts-Xl → end]`: timestamp handles I then have to resolve.
- Workaround: read the first IPC/CPU-activity times of the thread instead.

## Question: which thread does a report link's `thread=t` / `thread=k` open? (review)

- Command: `profiler-cli load "<profiler.firefox.com/from-url/…/marker-table/?marker=221865&thread=0&v=17>" --session browser-review-browser_eme.js-1` (as the review brief says), then `python3 profile-link.py --session browser-review-browser_eme.js-1 --marker m-193`
- Expected: a link for the marker, or a way to map the URL's `thread=` code to a `t-N` handle so the links in a report can be checked.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself` — a session loaded from a profiler link cannot build links, and nothing in `profiler-cli` decodes `thread=t` to `t-29`.
- Workaround: matched `markerIndex` from `marker info --json` on the candidate thread instead.
