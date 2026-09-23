# fx-tests feedback — test_videocontrols_onclickplay.html

## `fx-tests test` misses a crash that `fx-tests task` reports

- Command: `fx-tests test toolkit/content/tests/widgets/test_videocontrols_onclickplay.html --config debug-mochitest-chrome-1proc --history`
- Expected: a crash on 2026-09-19. `fx-tests task EHmmH_ZsQhq96HdgO0vZiw` (test-macosx1500-aarch64/debug-mochitest-chrome-1proc-2, autoland f1f4e9e45663) lists this test as `CRASH — 1 failing execution of 1 @ mozilla::layers::ImageBridgeParent::AllocPTextureParent`. The job has three `.extra` files with `MOZ_ASSERT_UNREACHABLE: Unexpected to be called!`.
- Got: `2026-09-19 (Sat) · 101 pass 0 fail 0 timeout 0 crash`. The test's totals (5 crash, 1 timeout, 1 fail) do not include it either, and neither does `--task-ids`.
- Workaround: found the job through the Treeherder API (`/api/jobs/?push_id=…`) on the push that landed the regression.
- Cost: the first push to carry the regression (the debug assertion, which is the direct evidence of which `AllocPTextureParent` branch rejected the texture) was invisible from `fx-tests test`.

## Question: "was this failure fixed by a landing, and which one?"

- Commands: `fx-tests test <path> --task-ids`, then `fx-tests task <id>`.
- What I needed: whether sheriffs classified the failing jobs, and as what. One job on the same push was "fixed by commit" `d2059835e40d` (a backout), and the chrome jobs were starred as intermittent on bug 1765783. Also: which pushes around the failing one ran the same config.
- Got: nothing about classification or neighbouring pushes. I scripted over the Treeherder API (`/api/jobs/?push_id=`, `/api/project/autoland/note/?job_id=`, `/api/project/autoland/bug-job-map/?job_id=`) to get them.
- What could have shown it: `fx-tests task` printing the Treeherder classification and note (e.g. "fixed by commit d2059835e40d", "intermittent, bug 1765783"). `fx-tests test --task-ids` flagging a cluster of failures on one push as "all on push X".

## Question: "which minidump is this test's crash?" (review-test_videocontrols_onclickplay.html)

- Commands: `fx-tests task EHmmH_ZsQhq96HdgO0vZiw`, then `fx-tests crash EHmmH_ZsQhq96HdgO0vZiw.0 <dump>`.
- What I needed: the dump ID of each crashed test, to read the stack of this test's own crash. The job has three crashes with the same signature, one per test.
- Got: `fx-tests task` lists each crashed test with its signature but no dump ID, so `fx-tests crash` has to be given a dump picked blind. The report under review cited `88187EE8-…` as this test's dump; it is `test_allowlisted_annotations.html`'s. Found by loading the job's resource-usage profile and reading the `minidump` field of each `CRASH` marker.
- What could have shown it: the dump ID next to each CRASH in `fx-tests task`, e.g. `CRASH — 1 failing execution of 1 @ … (dump 5B2750FC-…)`.
