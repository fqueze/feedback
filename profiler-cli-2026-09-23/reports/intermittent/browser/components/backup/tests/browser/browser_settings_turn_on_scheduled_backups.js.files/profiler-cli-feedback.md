## Question: "what part of the profile does this thread actually have samples for?"

- Command: `profiler-cli profile info --session …-1` on the per-test profile of VYJvaz8FQ3qEqLumSuEASA.
- Expected: a warning that the parent process's samples and markers start at ~95.6 s of a 99 s profile (the 537 MB buffer was filled by 7 M `DocAccessible::ContentRemovedNode` markers in the last ~2 s, so everything earlier was dropped).
- Got: `Full: 1m39s` and "CPU activity: 90% for 2450ms (1m36s - 1m39s)", which reads like "the thread was idle before 1m36s". Only `thread info` ("272 samples" over 99 s) and the missing TEST-* markers gave it away.
- Could have shown: per thread, the time range its samples and markers cover, and a flag when that is much shorter than the profile.

## Question: "when exactly did this marker start?"

- Command: `profiler-cli thread markers --category Test --list --limit 0` and `marker info m-50`.
- Expected: times to the millisecond, so markers can be put in order and matched against the resource-usage profile.
- Got: `t=1m36s` / `Time: 1m36s - 1m36s (832.31ms)`. Past one minute the time is rounded to the second.
- Workaround: `--json` and a Python one-liner to print `start`.

## Marker handles change with the query after a reload

- I reloaded a resource-usage profile to build links, ran a different `--search` than the first time, and reused the handle numbers from before. The links pointed at unrelated tests. The guide does say m-N is rebuilt per daemon, but it does not say the numbering also depends on the query. A warning in `profile-link.py` or `marker info` when the marker's name differs from the one expected would have caught it.
