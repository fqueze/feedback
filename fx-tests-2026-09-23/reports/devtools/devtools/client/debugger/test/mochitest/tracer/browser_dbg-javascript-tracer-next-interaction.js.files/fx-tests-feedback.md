## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-next-interaction.js --bugs`
- Expected: a "Bugs" section, saying "none found" if there are none.
- Got: the same output as without `--bugs`, with no Bugs section at all. With `--progress`,
  stderr shows "Reading 708 bug summaries…", so the lookup did run, but stdout does not say
  whether it found nothing or was skipped.
- Workaround: took it to mean no bug names the test.
