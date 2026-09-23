## `fx-tests test` misses 11 of the 17 failing jobs of a one-day regression

- Command: `fx-tests test toolkit/content/tests/chrome/test_menulist_scrollbar.xhtml --task-ids --limit 0` (and `--day 2026-09-02`)
- Expected: every Mac 15.30 job of autoland pushes 277603..277622 (2026-09-02 05:06..08:27 UTC) where the test failed.
- Got: 8 failures in 6 jobs (QNCOw, RmNo, Fkwz, VabR, b4Nz, GcPM). `fx-tests task <id> --passed --json` on the other Mac 15.30 chrome jobs of the same pushes finds the test failing 2 of 2 in 11 more jobs, all `test-macosx1500-aarch64{,-shippable}/opt-mochitest-chrome-no-nv*`: K9M_IOsTTBOfFSZHSI0VzA, Lrc1VtuJTgCRQuzMfLcWmA, OqWwtZyPRb-lbFxHaiTOjQ, AsMSttbST8WZQ5hGPlIZtg, dXr0HCMJTIOgD5X4qpf2nw, AEtXFF9iS7WKKjzk_H5Yrw, IC6OtEv_QIaF10D2XuKmUw, PBKzZJuDRQCj8f-Z3Qepvg, Plf1DUUdTHma9aXCWJh1Ng, QbjSNmoLS4W325d_IK0Dfw, OVFPn08TTSWTqx2l69Zucg. Real count: 30 failing executions in 17 of the 30 Mac 15.30 jobs that ran the test, not 8 in 6. Plf1 and Qbj are the jobs the backout comment links to.
- Workaround: Treeherder `/api/jobs/?push_id=` for each push, then `fx-tests task --passed --json` per job (script).

## Question needing a script: "in the pushes that carried this change, which jobs ran this test, and did it pass?"

- The question: for a regression that lived a few hours (landing to backout), the per-job outcome of one test across the pushes in between, passes included. That is what gives the real failure rate while the change was in the tree (17 of 30 jobs here, 15 of 18 on the no-Nova config), which the 21-day rate (1.1%) hides completely.
- Commands: Treeherder API per push + `fx-tests task <id> --passed --json` per job, walked for the test path.
- What could show it: `fx-tests test <path> --revision-range <from>..<to>` (or `--since-push`/`--until-push`) with per-config pass/fail counts and the task IDs of passing runs as well as failing ones.
