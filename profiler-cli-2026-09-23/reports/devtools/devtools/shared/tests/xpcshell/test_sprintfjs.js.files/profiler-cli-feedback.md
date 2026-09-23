## Which test markers were in flight at time t

- Question: "which tests were running at the moment the first launch failure happened (t=63.896 s)?"
- Command used: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a Python filter for `start <= t <= start+duration`.
- Expected: something like `thread markers --search name:test --overlapping 63.896` (or `--at`). `zoom push a,b` lists markers that *start* in the range, not the long intervals already running, so it misses the tests that began earlier and were still running.

Correction to the entry above: `zoom push` does keep interval markers that started before the range and overlap it (a zoom on 97–125 s listed test markers that started at 52 s). So `zoom push 63.896,63.9` followed by `thread markers --search name:test --list` would have answered this without the script. What cost time was not knowing that. The guide's ZOOM section could say that overlapping intervals are included.
