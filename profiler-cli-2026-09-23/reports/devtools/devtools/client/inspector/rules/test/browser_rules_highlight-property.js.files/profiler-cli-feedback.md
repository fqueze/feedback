## `--search` does not match the text printed for DOMEvent markers

- Command: `profiler-cli profile markers --search "resize - window,inspector:getWalker()" --limit 0`
- Expected: the `DOMEvent  resize - window` rows that `thread markers --list` prints, since that is the text shown.
- Got: only the getWalker markers; the printed "resize - window" label is not searchable.
- Workaround: `--search eventType:resize`, found by guessing the payload key. The question was "on each thread, did the content window resize before or after the walker was created?"; a hint in the no-match case that DOMEvent is searched by `eventType:` / `target:` would have saved two round trips.

## (review) "For each toolbox opening, did the content window resize come before or after the walker?" needed a script

- Command: `profiler-cli profile markers --search "eventType:resize,inspector:getWalker()" --limit 0 --json`, then Python to bucket the markers by the preceding `INFO Opening the toolbox` on the parent thread and print offsets.
- Question: the order and offset of two marker kinds on content threads, relative to each occurrence of an anchor marker on another thread.
- What could have shown it: a `--relative-to <search>` (or `--anchor`) option on `profile markers --list` that prints each match's offset from the latest preceding anchor marker, grouped per anchor. It would also make the duration outlier (one resize at 375 us against a 2-3 us median) visible without sorting JSON by hand.
