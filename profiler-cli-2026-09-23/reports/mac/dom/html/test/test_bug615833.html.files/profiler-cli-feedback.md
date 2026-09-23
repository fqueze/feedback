## Thread-scoped marker search found nothing, with no hint that other threads match

- Command: `profiler-cli thread markers --category Test --search test_bug615833 --list --limit 0 --session test_bug615833.html-1` with t-0 (parent GeckoMain) selected, on a mochitest-plain profile.
- Expected: the test's TEST-PASS / TEST-UNEXPECTED-FAIL log.
- Got: `0 markers (filtered from 1110296)` / `No markers match the specified filters.` For mochitest-plain the TestStatus markers are on the content process main thread (21 matches on t-20), which the output does not mention.
- Workaround: `profiler-cli profile markers --search test_bug615833` to find the thread.
- Suggestion: when a thread-scoped search is empty, print the other threads with matches, as `profile markers` does ("Matches by thread").

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session test_bug615833.html-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message named `PROFILER_CLI_SESSION_DIR` as the fix, which worked (`/Users/florian/.sandbox/tmp/profiler-cli`). One wasted call; `profile-link.py` honours the same variable.

## Marker search does not match the label as displayed (review-test_bug615833.html)

- Command: `profiler-cli thread markers --search "[Select]" --list --limit 0 --session review-test_bug615833.html-1` (also `--search "Select]"`), on t-0 zoomed to 13.45-13.50 s.
- Expected: the `ReceiveMessage` / `SendAsyncMessage` markers the list shows as `[Select] Forms:ShowDropDown`.
- Got: `0 markers ... No markers match the specified filters.` `--search "Forms:"` and `--search "Select"` both match them, so the label seems to be assembled from separate fields (actor, message) that are searched one by one.
- Workaround: search on a single field's text (`Forms:`). One wasted call; a quote copied from the list output silently finds nothing.
