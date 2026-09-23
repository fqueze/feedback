## Question: does any bug name this test?

- Command: `fx-tests test toolkit/components/satchel/test/browser/browser_popup_mouseover.js --bugs`
- Expected: a "Bugs" section, saying "none" when the lookup found nothing.
- Got: output identical to the run without `--bugs` — no section at all, so "looked and found none" and "did not look" read the same.
- Workaround: `--bugs --json`, then read `annotatedBugs` (`[]`), and a Bugzilla REST summary search to be sure.
- What the default output could show: a `Bugs: none annotated in the window` line when `--bugs` is given.
