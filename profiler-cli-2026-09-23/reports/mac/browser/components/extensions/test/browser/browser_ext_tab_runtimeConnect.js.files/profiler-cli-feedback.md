# profiler-cli feedback (browser_ext_tab_runtimeConnect.js)

## Question: which tests ran in this browser session, in order, with their status?

- Command: `profiler-cli thread markers --thread t-0 --search "name:test" --category Test --list --limit 0`
- Expected: the 33 markers named `test` (`PASS — <path>` / `FAIL — <path>`).
- Got: 2,024 markers, because `name` is also a payload key on Text markers (documented caveat),
  so every `TestUtils`/`BrowserTestUtils`/`task` marker matched. `--group-by name` shows
  `test: 33 markers` but cannot list them.
- Workaround: `--search "browser/components/extensions/test/browser/" --list --limit 0 | grep ' test '`.
- What would have answered it: a way to match the marker name exactly (e.g. `markername:test`),
  or `--group-by name` followed by listing one group's markers.
