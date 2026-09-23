# profiler-cli feedback

## "Which page is innerWindowID 2?"

- Command: `profiler-cli marker info m-62 --session <id>` on a `RefreshDriverTick waiting for paint` marker, whose payload is `innerWindowID: 2`.
- Expected: some command that maps an innerWindowID to its page URL, like `profile pages`, or the URL printed next to `innerWindowID` in `marker info`.
- Got: only the number. None of `profile info`, `profile meta` or `marker info` shows the profile's `pages` table.
- Workaround: I curled the whole profile (177 MB unzipped) and read `pages[]` in Python. That found `chrome://browser/content/browser.xhtml`.

## "At what second did this marker happen?"

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0`
- Expected: timestamps precise enough to line markers up, which needs about 1 ms. I needed that for a 5.5 s wait.
- Got: `t=2m8s` / `t=2m14s`. Every marker in the test log shows the same whole second.
- Workaround: `--json` and reading `start`. The list could print `t=128.329s` (or `2m8.329s`).

## "Which page is innerWindowID 233?" (again, from the reviewer)

- Command: `profiler-cli profile markers --search RefreshDriverTick --session <id>` (zoomed to 128.4–133.8 s) showed a second thread, t-45 (Web Content 8/8), ticking `RefreshDriverTick waiting for paint` with `innerWindowID: 233`.
- Expected: the page URL for that ID, as in the entry above.
- Got: only the number.
- Workaround: streamed and gunzipped the whole artifact through a Python regex for `{"tabID": ..., "innerWindowID": 233, "url": ...}`: `about:blank`, tabID 60. About a minute and 177 MB for one string.
