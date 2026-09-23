## `--bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/browser-trustPanel/browser_trust_panel_privacy_metrics_alignment.js --bugs`
- Expected: a Bugs section, saying "no bug names this test" when there is none.
- Got: the ordinary `fx-tests test` output with no Bugs section and no line about bugs, so I could not tell "none found" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<file name>`, which returned `{"bugs":[]}`.
