## Time units in `zoom push` (browser_console_webconsole_private_browsing.js report)

- Command: `profiler-cli zoom push 18m,24m55s --session ...`
- Expected: 18 min to 24 min 55 s, the format the tool itself prints (`t=22m24s`).
- Got: silently zoomed to 18s-24s ("Pushed view range: ts-1 (18s) to ts-2 (24s)"), then 0 markers.
- Workaround: seconds (`1000,1495`).

## Listing the `test` markers of one manifest in order

- Question: which tests ran in this browser session, in order, with status (resource-usage profile)?
- Command: `profiler-cli thread markers --search 'type:Test' --list --limit 0` matched thousands of `output` markers too; a bare path search also returns every `output` marker of those tests.
- Expected: a way to keep only markers named `test` (name-only match).
- Workaround: `--json` and a Python filter on `name == 'test'`.

## Searching for a `test` marker by the label the list shows (review-browser_console_webconsole_private_browsing.js)

- Command: `profiler-cli thread markers --search "PASS — devtools/client/webconsole/test/browser/browser_console_webconsole_private_browsing" --list --limit 0 --session ...`
- Expected: the `test` marker, whose list line reads `PASS — devtools/client/.../browser_console_webconsole_private_browsing.js`.
- Got: "No markers match the specified filters."
- Workaround: search the file name alone, then filter on `\stest\s` in the output.
