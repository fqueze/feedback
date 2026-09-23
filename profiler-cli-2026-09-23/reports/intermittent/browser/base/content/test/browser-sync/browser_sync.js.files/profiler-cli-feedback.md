## `thread samples` after `zoom pop` still reports the popped range

- Command: `profiler-cli zoom push 41.9,144 --session S`, `zoom push m-974 --session S` (a 1.193s marker), `thread samples ...`, then `zoom pop --session S`, then `thread samples --session S --include-idle`.
- Expected: samples over the remaining 41.9–144 zoom (1m42s).
- Got: the header said `View: ts-g→ts-zz (1m42s)` and `status` agreed, but the body was byte-identical to the 1.193s zoom (51 samples; `thread functions` said "72.5% of view, 0.7% of full"). `thread samples` without `--include-idle` also showed 51.
- Workaround: `zoom clear` then re-`zoom push 41.9,144`: 3807 samples, as expected.
- Cost: nearly reported a 1s window's numbers as the whole run's.

## Question: "average machine CPU over a time range" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script averaging `fields[0].value` over each TV step's range.
- The question: was the machine saturated during the chaos-mode run compared with the normal runs? `profile info` said "No significant activity" (no CPU counter in resource-usage profiles), and the marker aggregate gives only durations, not the payload's CPU %.
- What would have answered it: `thread markers --search "name:CPU Use"` in aggregate mode reporting mean/max of numeric fields (cpuPercent) for the current zoom, so `zoom push <range>` + one command gives the machine load there.

## Same profile, new daemon: same `m-N` names a different marker, silently

- Command: load a resource-usage profile, `thread markers --search "name:test,test verification step,..." --list`, stop; reload in a new session, `thread markers --search "name:test,MOZ_CHAOSMODE" --list`, then `profile-link.py --marker m-8` using the handle from the first session.
- Expected: an error, or the same marker (the guide says m-N is not stable across daemons, so an error would be the safe outcome).
- Got: a valid link to another marker. Caught only because the marker indices in the URLs were out of time order.
- Workaround: re-list in the live session and link from that listing. A handle scheme that includes a session nonce (e.g. `m-8@3f`) would make stale handles fail loudly.
