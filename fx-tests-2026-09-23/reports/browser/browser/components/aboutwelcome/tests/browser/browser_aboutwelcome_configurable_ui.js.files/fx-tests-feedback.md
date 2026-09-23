## Question: "do these two tests fail in the same jobs?"

- Command: `fx-tests test <A> --task-ids --limit 0` and `fx-tests test <B> --task-ids --limit 0`, then `rg -o '^\s+\S+\.0' | sort` on each and `comm` to intersect.
- Expected: a way to see, for a victim test, which other tests failed in the same jobs (e.g. `fx-tests test <path> --co-failures`: per other test, in how many of this test's failing jobs it also failed, and whether it ran before this one).
- Got: two task-ID lists to intersect by hand. The answer here (27 of 27 jobs of the main failure mode also had browser_aboutwelcome_campaign_actions.js fail) is the single strongest piece of evidence for a leaker, and `fx-tests task` only shows it one job at a time.
- Workaround: text-scraping and `comm`, as above.
