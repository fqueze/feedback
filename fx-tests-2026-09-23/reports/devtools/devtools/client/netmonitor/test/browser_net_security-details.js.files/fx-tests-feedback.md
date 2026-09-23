## Which config the skip-if applies to (coverage table hides it)

- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_security-details.js --coverage`
- Expected: the one config where the test is skipped named, since that is the config a `skip-if` removal has to be verified on.
- Got: "linux 12/13 ran — 1 scheduled but skipped" without naming it; the table is sorted by runs, so the skipped config (0 runs) is always cut off by the default limit ("… 16 more").
- Workaround: rerun with `--limit 0`, which showed `test-linux2404-64/debug-mochitest-devtools-chrome-http3 ... skipped`.
- Suggestion: always print skipped-only configs, or name them on the "scheduled but skipped" line.
