## `fx-tests test --task-ids` misses most failing jobs of a one-day regression

- Command: `fx-tests test browser/components/urlbar/tests/browser-UrlbarInput/browser_a11y.js --task-ids --limit 0`
- Expected: every job on 2026-09-19 where the test failed.
- Got: 9 jobs / 13 failures. Treeherder for autoland pushes 280237-280241 alone shows 15 `test-linux2404-64/opt-mochitest-browser-chrome-no-nv-5` jobs where this test failed (e.g. fgRIoNHHQFmTW6TMyJSwEQ, cSljEw6eTHKB-v_I148MDQ, DHFdOn10Qiu3wUz8tXZcKw, SR6hy1EURRau2WuUIw-YdQ, VFKTgNIJSpeLi9J7ynB91Q, SDU4xXQHTCCgoZw7m_AyTA, OVlHDVrxRym_THUunZrBWQ, WzvG2fvXRkKRBsnmEWWDTA are missing; `fx-tests task <id>` on each confirms the failure), and the sheriff's backout comment cites asan bc10 task eGGYdDeXSUaRU7qnROKUsA, also missing. So the per-config rate (1.9%) and counts understate it by about 2x.
- Question it could not answer: "how often did this fail on the revisions that carried the regressor?" — a per-revision pass/fail count (or `--revision <rev>`) would have answered it without scripting over the Treeherder jobs API.
- Workaround: Treeherder `/api/jobs/?push_id=` per push, then `fx-tests task <taskId>` on every job of the chunk.
