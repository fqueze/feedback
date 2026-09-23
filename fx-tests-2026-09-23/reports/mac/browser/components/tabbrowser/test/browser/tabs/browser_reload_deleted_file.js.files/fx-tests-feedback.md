## Question: which jobs on config X have a per-test profile covering a browser session of manifest M (to compare with a config where test T passes)

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs --config windows11-64-25h2/debug-mochitest-browser-chrome-vt,...`
  and `fx-tests failures --config ... --path browser/components/tabbrowser/test/browser/tabs`
- Expected: the failing tests of that directory on those configs, with task IDs, so I can pick any per-test profile from a vt session on Windows/Linux (where `browser_reload_deleted_file.js` passes) and compare the browser state with the macOS one.
- Got: `test` refuses a directory ("is not a test path, and 198 tests match it"); `failures` refuses `--config` (the issues file has no job names).
- Workaround: a shell loop running `fx-tests test <each test in the manifest> --config ...` over ~120 tests to find one with vt failures, then `fx-tests task <id> --profiles`. Took many minutes.
- What would have answered it: `fx-tests test <dir> --config <cfg> --task-ids` listing, per failing test in the directory, the task IDs (a per-test profile of any of them covers the session up to that test).
