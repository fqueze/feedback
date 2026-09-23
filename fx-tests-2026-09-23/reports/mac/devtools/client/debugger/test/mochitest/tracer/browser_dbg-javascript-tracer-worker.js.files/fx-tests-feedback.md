## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-worker.js --bugs`
- Expected: a Bugs section, or a line saying no bug names the test.
- Got: the normal `test` output with no Bugs section at all and exit 0, so "no bugs" looks the same as "the flag was ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>` to confirm there was none.

## Question: "is this still failing, and since when did it stop, on the worst config?"

- Command: `fx-tests test <path> --history` gives per-day counts over every config (about 300 runs a day), which hides a stop on a config with about 15 runs a day. I had to run `--history --config <config>` for each failing config separately.
- The default output could say it outright: for each failing config, the date of its last failure and the runs since then (here: last failure 2026-09-10, 0/122 since).
