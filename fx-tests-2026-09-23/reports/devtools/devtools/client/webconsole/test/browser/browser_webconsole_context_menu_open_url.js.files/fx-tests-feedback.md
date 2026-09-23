## Did the try push exercise the removed skip-if? (browser_webconsole_context_menu_open_url.js)
- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_webconsole_context_menu_open_url.js --all-jobs`
- Expected: besides the per-config pass table, a line saying the test's skip-if on central (`... && debug && http3`, the only config it is skipped on: `test-linux2404-64/debug-mochitest-devtools-chrome-http3`) matches no job scheduled on this push.
- Got: 7 configs, 21/21 passed, nothing about the skipped config. A reader concludes "the skip can go".
- Workaround: `fx-tests test <path> --coverage --limit 0` to find the skipped config, then the Treeherder jobs API to confirm no http3 job existed on the push.

## `--bugs` prints no bug section
- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_context_menu_open_url.js --bugs`
- Expected: a "Bugs" section, or "no bug names this test".
- Got: the same output as without `--bugs`; cannot tell "none found" from "flag ignored".
- Workaround: read the bug number from the manifest comment (Bug 1829298).
