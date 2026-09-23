## The test's own log, for a mochitest-plain test

- Command: `profiler-cli thread markers --category Test --search test_pixel_pack_buffer --list --limit 0` on the parent process GeckoMain (t-0), as the brief suggests.
- Expected: the test's TEST-PASS / TEST-UNEXPECTED-FAIL markers.
- Got: `0 markers (filtered from 675419)`. For a mochitest-plain test the TestStatus markers are on the content process GeckoMain (`http://mochi.test`, t-13 here), not the parent.
- Workaround: `profiler-cli profile markers --search pixel_pack --limit 0` found which thread held them; then `thread select` that thread and `zoom push` the test's time range. An empty result on the selected thread could mention that N matching markers exist on other threads (as `profile markers` already counts per thread).
## Which m-handle is the `marker=N` of the link I loaded (review-test_pixel_pack_buffer.html)

- Command: `profiler-cli load '<profiler.firefox.com URL with marker=76356&thread=d>' --session <s>`, then to check each of the report's links `marker info <m-…> --json` piped through a Python one-liner to print `markerIndex`, one guessed candidate at a time.
- Expected: `load` to say which handle the URL's `marker=N` is (as it already selects the URL's thread), or `thread markers --list` to show each marker's index, so a link's marker can be found without guessing.
- Got: only the selected thread. To map 8 marker indices from the report's links (two threads), I had to guess candidates from the `--list` output and run `marker info --json` on each until the indices matched: about 5 extra calls plus a script.
- Workaround: `marker info m-a m-b … --json | python3 -c '…print(markerIndex)…'`.
