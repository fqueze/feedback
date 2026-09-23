## How busy was the machine while the test hung (resource-usage profile)

- Question: "was the machine saturated during this 53 s window" on a job's `profile_resource-usage.json`.
- Command: `profiler-cli profile info --session <s>` on the resource-usage profile of task C8P3uVVhS_G6Y7H0-EzwYQ.
- Expected: a CPU summary for the whole profile or the zoomed range.
- Got: `CPU activity over time: No significant activity.` and `counter list` says `No counters in this profile.`, although the profile holds 1,301 `CPU Use` markers with `idle_pct`/`user_pct` fields. It reads as "the machine was idle" for the wrong reason.
- Workaround: `zoom push 574,627`, then `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python loop averaging `data.idle_pct` (mean 99.7 %, min 81.7 %).
- What could show it: treat the resource-usage `CPU Use` markers as a counter (`counter info` with min/mean/max over the zoom), or have `profile info` summarize them.
