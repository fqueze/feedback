## `--bugs` finds no bug although one names the test exactly

- Command: `fx-tests test dom/media/autoplay/test/browser/browser_autoplay_policy_touchScroll.js --bugs` (also with `--json`)
- Expected: bug 2046618, "Intermittent dom/media/autoplay/test/browser/browser_autoplay_policy_touchScroll.js | testTouchScroll - media is playing. - true == false ...", open since 2026-06-10.
- Got: the normal output with no bugs section at all, not even a "no bug found" line; `annotatedBugs: []` in the JSON. `fx-tests intermittent --bug 2046618 --since 30 --tree all` says there are no sheriff annotations, which fits: every failure in the window passed on the harness retry, so the jobs stayed green and nobody starred them. So `--bugs` seems to come only from annotations, and a test whose failures all pass on retry never shows its bug.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"`.
- Question the output could have answered: "is there a bug for this test?" Search bug summaries for the test path, or at least say that nothing was annotated in the window.
