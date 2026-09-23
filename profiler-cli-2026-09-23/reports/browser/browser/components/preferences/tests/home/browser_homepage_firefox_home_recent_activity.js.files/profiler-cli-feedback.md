## Daemon died silently on a marker search over a 6.9M-marker thread

- Command: `profiler-cli thread markers --session browser-recent_activity-1 --search "-category:Accessibility,-name:Preference Read,-name:~ScriptLoadRequest,-name:DOMEvent" --list --limit 0` on the parent GeckoMain of
  `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/AyivmzKBQkuXN7Y7EiPaYw/runs/0/artifacts/public/test_info/profile_browser_homepage_firefox_home_recent_activity.js.json`
  (6,946,620 markers, 99.8% `DocAccessible::ContentRemovedNode` from the a11y engine).
- Expected: the non-a11y markers, or an error.
- Got: no output at all, exit 0; the next command said `Unknown session`. The daemon log ends on `Received message: command` with no error (likely OOM).
- Workaround: reload, and always combine a zoom with a positive `--search`/`--category`, never exclusions alone on such a thread.
- Suggestion: report the daemon's death to the client (non-zero exit, "daemon exited, likely out of memory") instead of empty stdout; and possibly warn upfront when a thread has millions of markers of one name.

## Question: "over what time range does this thread actually hold data?"

- Command: `profiler-cli profile info --session browser-recent_activity-1` on the per-test profile of task AyivmzKBQkuXN7Y7EiPaYw.
- Expected: some sign that the parent GeckoMain only holds the last ~2.5 s (buffer overwritten by 6.9M a11y markers), since the header says `Full: 2m59s`.
- Got: `Full: 2m59s` and a CPU total; I only found out after `thread markers --list --limit 5 --json` showed the earliest marker at 167 s and samples in a 9.6 s zoom numbered 146. Long interval markers that *end* in the window (a 9.6 s Runnable, an 11 s `task`) make the retained range look longer than it is.
- Suggestion: print per thread the first and last sample/marker time ("data from 176.5 s to 179.0 s") in `profile info` / `thread info`, and flag when it is much shorter than the profile.

## Question: "how long did each add_task take, in this run and in the retry?"

- Command: `profiler-cli thread markers --session <resource-usage> --search "Entering test,Leaving test" --list --limit 0 --json` piped into a script to subtract Entering/Leaving pairs.
- The default output lists the INFO lines, but per-task durations needed a script; a `--group-by`-like "pair Entering/Leaving test" view, or the harness emitting a `task` interval marker into the resource-usage profile (it already does in per-test profiles), would answer it directly.
