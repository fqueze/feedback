# fx-tests feedback — test_img_src_causing_reflow.html

## `--bugs` prints nothing, not even "none found"

- Command: `fx-tests test layout/style/test/test_img_src_causing_reflow.html --bugs`
- Expected: a Bugs section, either listing bugs that name the test or saying none do. Bug 1790524, "Intermittent layout/style/test/test_img_src_causing_reflow.html | single tracking bug", names it; it is RESOLVED INCOMPLETE since 2025-02-03.
- Got: the same output as without `--bugs`. There was no Bugs section and no "no bug found" line, so you cannot tell "no bug" from "the flag did nothing".
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=test_img_src_causing_reflow'`.
- Would help: list closed bugs too, marked as closed. A closed tracking bug for a test that still fails is what a sheriff needs to reopen.
