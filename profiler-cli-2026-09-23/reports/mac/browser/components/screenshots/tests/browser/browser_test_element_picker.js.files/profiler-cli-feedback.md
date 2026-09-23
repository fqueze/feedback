## `profile markers --search Load` on an 87-thread profile did not finish in 120 s

- Command: `profiler-cli profile markers --search 'Load' --session elpick-2` (per-test mochitest profile, 87 threads, ~816k markers on the parent main thread)
- Question: which content process loaded `rtl-test-page.html` last.
- Expected: an answer in seconds, or a `--limit`ed sweep.
- Got: no output after 120 s; had to kill it. The daemon stayed busy until it finished.
- Workaround: `--search 'rtl-test-page'` (a rarer term) answered in a few seconds.

## `thread markers --list` does not show which IPC message an IPCIn/IPCOut marker is

- Command: `profiler-cli thread markers --search IPCIn --list --limit 0 --session elpick-1`
- Question: which IPC message from the parent resized the tab (was `PBrowser::Msg_UpdateDimensions` the first one this tab got).
- Got: every row reads just `IPCIn`, with no message type; I needed `marker info` on each, then a script over `--json` to print `fields.messageType`.
- Could have shown: the message type (`PBrowser::Msg_UpdateDimensions`) in the row's description, as `Runnable` rows already do with their runnable name.

## `thread markers --list` rounds every start time to the second, even inside a 120 ms zoom

- Command: `profiler-cli zoom push 85.54,85.66 --session review-browser_test_element_picker.js-1`, then `profiler-cli thread markers --search RefreshDriverTick --list --limit 0 --session review-browser_test_element_picker.js-1`
- Question: which parent refresh ticks fall between `TabOpen` and the tab's first `UpdateDimensions`, 26 ms apart.
- Expected: start times precise enough to order markers within the view, e.g. `t=85.5608s`.
- Got: every row reads `t=1m26s`, so the rows cannot be ordered against each other or against another thread's markers.
- Workaround: `--json`, and a script printing `start/1000` with four decimals.
- Could have shown: a precision chosen from the view's duration (ms-level inside a sub-second zoom), as `profile-link.py` and the report need anyway.
