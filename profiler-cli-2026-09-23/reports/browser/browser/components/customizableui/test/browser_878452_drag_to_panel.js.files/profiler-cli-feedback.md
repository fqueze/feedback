## Question: which tests ran in this browser session, in order, with their status?

- Command: `profiler-cli thread markers --session S --search "name:test" --category Test --list --limit 0`
- Expected: the per-test `test` interval markers (`PASS — browser/.../browser_x.js`), one per test.
- Got: 361 markers, mostly `TEST-PASS`/`INFO` rows, because `name:` also matches the Text payload key (the guide warns about this, but there is no name-only filter).
- Workaround: `--search "browser/components" --category Test --list --limit 0 | rg " test  "`.
- What would have answered it: a name-only filter (e.g. `marker-name:test`), or a `profile tests` summary listing each `test` marker with status and time range.

## Question: which window/document is the target of this DOMEvent? (review-browser_878452_drag_to_panel.js)

- Command: `profiler-cli marker info m-5087 m-9245 --session S` on `resize - window` DOMEvent markers.
- Expected: the page URL of the window (browser.xhtml vs about:firefoxview vs a new window).
- Got: `Event Target: window` and a bare `innerWindowID: 76` under "Other payload fields"; there is no command that shows the profile's `pages` table.
- Workaround: compared innerWindowIDs against a resize known to be the browser window's (the fullscreen test's), to tell the chrome window (2) apart from others.
- What would have answered it: resolve `innerWindowID` to the page URL in `marker info` (and in the `--list` rows for DOMEvent), or a `profile pages` command.
