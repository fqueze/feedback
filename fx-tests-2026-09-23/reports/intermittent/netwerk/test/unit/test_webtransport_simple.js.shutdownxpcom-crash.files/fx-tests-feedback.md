## `--bugs` prints nothing at all

- Command: `fx-tests test netwerk/test/unit/test_webtransport_simple.js --bugs`
- Expected: a Bugs section naming bug 1816439 ("Intermittent netwerk/test/unit/test_webtransport_simple.js | single tracking bug", open, assigned), or an explicit "no sheriff-annotated bug names this test in the window".
- Got: the normal output, with no Bugs section and nothing on stderr. Indistinguishable from the flag being ignored.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=test_webtransport_simple'`.

