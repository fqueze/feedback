## Question: how busy was the machine's disk during each test of a job (resource-usage profile)?

- Command: `profiler-cli thread markers --search "name:IO" --list --limit 0 --session <resource-usage session>` (and the same for `name:CPU Use`).
- Expected: a way to see disk write throughput and iowait over a range, or per `test` marker, e.g. an aggregate of the `IO` markers' `Written` field and the `CPU Use` markers' `IO Wait %` in the zoomed range, the way `counter info` summarises counters.
- Got: one row per 100 ms marker with no field values in the list; the values only appear with `marker info` one marker at a time. 17,618 IO markers per job.
- Workaround: `--json`, then a Python script summing `Written` and averaging `iowait_pct` per `test` marker, and finding sustained bursts. That is what showed a 2.6 GB, 16 s write burst at ~43 s into each of three failing jobs, during this test only.
- What would have answered it: `thread markers --search name:IO --sum-field Written` or the resource profile's CPU/IO exposed as counters (`profile info` said "No significant activity" and `counter list` said "No counters in this profile", although CPU and IO were recorded as markers).

## `--search` does not match a TimingDistribution marker's displayed metric name

- Command: `profiler-cli thread markers --search "urlbar.autocomplete" --list`
- Expected: the `TimingDistribution::start urlbar.autocomplete_first_result_time` markers, which is how the list prints them.
- Got: no match; the payload stores `cat: urlbar` and `id: autocomplete_first_result_time` separately, so the printed dotted name is not searchable.
- Workaround: `--search "id:autocomplete_first_result_time"`, after reading `marker info --json`.

## (review) Loading the raw Taskcluster URL selects a content thread, not the parent main thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/HoqZNJ1BSlC3hrVtOPdaZg/runs/0/artifacts/public/test_info/profile_browser_aichat_content_overflow.js.json' --session X`, then `thread markers --search name:SpecialPowers`.
- Expected: the parent process GeckoMain selected, as when loading the profiler link with `thread=0`.
- Got: `t-19 (GeckoMain, Privileged Content)` selected; my searches silently returned that thread's markers.
- Workaround: `thread select t-0`.
