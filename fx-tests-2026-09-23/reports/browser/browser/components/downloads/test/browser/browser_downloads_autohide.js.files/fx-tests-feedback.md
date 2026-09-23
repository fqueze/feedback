## Question: which other tests fail in the same jobs as this one (the leaker suspect)

- Commands: `fx-tests test <victim> --task-ids --limit 0` and `fx-tests test <suspect> --task-ids --limit 0`, then compared the two task lists by eye (18 of 18 victim tasks were in the suspect's list), then `fx-tests task <id> --passed` on the suspect-only tasks to check the victim ran and passed there.
- Expected: `fx-tests test <path>` to say which tests co-fail in the same jobs, and how often they fail before this one in the same browser.
- Got: nothing about co-failures; `fx-tests task` shows it for one job only.
- Could have shown: a "Fails together with" section: tests failing earlier in the same job, with counts out of this test's failing jobs, and their counts in jobs where this test passed.
