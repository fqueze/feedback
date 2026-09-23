## Question: did all of these failures come from one push?

- Command: `fx-tests test toolkit/content/tests/widgets/test_videocontrols_focus.html` (and `--task-ids --limit 0`)
- Expected: the verdict to say that all 6 crashes came from a single revision (autoland 0a3b36a2cebf), which is the whole diagnosis for a regression that was backed out within hours.
- Got: "Verdict: intermittent. Fails on 3 configurations; worst ... 0.4%". `--task-ids` lists each job once per execution (6 rows for 3 jobs) and shows no revision; I had to run `fx-tests task` on each of the three jobs to see they share `autoland 0a3b36a2cebf`. `fx-tests intermittent --bug` printed the identical push time, which was the first hint.
- Workaround: `fx-tests task <id>` per job, then the hg pushlog.
- Could have shown: a revision column in `--task-ids`, and a verdict like "all failures from 1 push (0a3b36a2cebf): likely a regression, check its neighbours for a backout".
