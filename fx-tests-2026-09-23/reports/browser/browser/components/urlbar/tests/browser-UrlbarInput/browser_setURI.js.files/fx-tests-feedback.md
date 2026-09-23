## Question: which bug tracks this test?

- Command: `fx-tests test browser/components/urlbar/tests/browser-UrlbarInput/browser_setURI.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test". The manifest's `skip-if` names bug 1773790, whose summary uses the test's old path `browser/components/urlbar/tests/browser/browser_UrlbarInput_setURI.js`.
- Got: output identical to the run without `--bugs`. There was no Bugs section and no "none found" line, so it was unclear whether the flag did anything.
- Workaround: read the bug number from the manifest's `skip-if` comment. It would help to also match bugs by the `skip-if` comment's bug number, or by the test's former path.
