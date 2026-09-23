## Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --search "ai-window:,TabSelect,..." --list --limit 0 --session <id>` on a per-test profile whose test runs at t=16m17s.
- Expected: start times precise enough to order events that are 1-50 ms apart (e.g. `t=977.152s` or `16m17.152s`).
- Got: every row shows `t=16m17s` — one-second resolution — so markers 2 ms apart are indistinguishable, even inside a 75 ms zoom. The list is sorted, but the gaps (the evidence in a race) are invisible.
- Workaround: `--json` and a Python script printing `start/1000` with 3 decimals.
- What the output could have shown: sub-second times (ms precision) whenever the zoom range or the profile duration makes the second-level format ambiguous, or times relative to the zoom start.

## Question: "which of several searches' markers interleave, in one chronological list?"

- Command: `profiler-cli thread markers --search "smartWindow.sidebar,quickPromptDisplayed,ai-window:,TabSelect,setTimeout handler with interval 50ms,TEST-" --list`
- Expected: only markers matching those terms in name/label.
- Got: bare terms also match payload values anywhere, so `INFO`/`TEST-` pulled in hundreds of `Preference Read`/IPC markers whose payload contains "info"/"test" (e.g. `browser.geolocation.warning.infoURL`). Had to post-filter in Python.
- Workaround: `--json` + filter by `name`/`label`.
- What the output could have shown: a `label:` field selector (or case-sensitive match) to match only the displayed marker label.

## (review) Load of a 1.9M-marker profile "fails" while it is still loading

- Command: `PROFILER_CLI_SESSION_OWNER=… profiler-cli load "<profiler.firefox.com from-url link>" --session <id>`
- Expected: the command waits until the profile is ready, or says that loading goes on in the background.
- Got: exit 1 with `Profile load timeout after 60000ms` then `Profile still loading, try again shortly`. The daemon kept going, and `status` worked about a minute later.
- Workaround: poll `status` in a loop, or set `PROFILER_CLI_LOAD_TIMEOUT_MS=400000`.

## (review) Question: "which thread is the parent process main thread?" (default selection after load)

- Command: `profiler-cli load https://firefox-ci-tc…/profile_browser_smartwindow_tab_switching_starters-2.js.json --session <id>`, then `thread markers …`
- Expected: GeckoMain of the Parent Process selected by default, as it was for the two other per-test profiles.
- Got: `t-21 (GeckoMain, Privileged Content)`. My first marker query came back empty without saying why, and I had to run `profile info --search GeckoMain` and `thread select t-0`.
- What the output could have shown: pick the parent process's GeckoMain by default, or have `load` print which thread it selected and why.
