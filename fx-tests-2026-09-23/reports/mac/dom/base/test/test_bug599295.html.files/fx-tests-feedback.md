# fx-tests feedback from test_bug599295.html

## `fx-tests test` misses failing jobs on other platforms of the same push

- Command: `fx-tests test dom/base/test/test_bug599295.html` (also `--task-ids --limit 0`)
- Expected: every job that failed the test in the window, on every config.
- Got: "Fails on 1 configuration", 4 failures in 2 jobs, both test-macosx1500-aarch64/opt-mochitest-plain.
  But `fx-tests task` on other jobs of autoland push 2040758 (rev 2f0bd86b184e, 2026-09-18) shows
  the same test failing twice (first run and retry) there too:
  `UDYdKf7jRVuZDBYkG8BTzg` (test-linux2404-64/opt-mochitest-plain-4),
  `LanBcTKnSKWT2ejNHxBPQg` (test-windows11-64-25h2/opt-mochitest-plain-1),
  `VjVvpKpeRGeh3YdVlg5Nog` (test-windows10-64-2009-qr/opt-mochitest-plain-4),
  `at2MABDHSI-KossQYhf5Dw` (test-android-em-14-x86_64/opt-geckoview-mochitest-plain-4).
  None of them is in `fx-tests test`'s counts or task list. The failure came from a patch that was
  backed out (bug 2045704); maybe jobs sheriffs classified as "fixed by commit" are dropped, but
  then the Mac ones should be too. Either way, "fails on one config" was wrong and would have sent
  a diagnosis towards a Mac-only cause.
- Workaround: listed the push's jobs from the Treeherder API
  (`/api/jobs/?push_id=<id>`) and ran `fx-tests task` on each failed one.
- Question the output could have answered: "on which configs did this test fail in this push?"
  (`fx-tests test <path> --push <rev>` or a per-push breakdown).

## `fx-tests test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test dom/base/test/test_bug599295.html --bugs`
- Expected: a line such as "No bug names this test".
- Got: the normal output with no bugs section at all, identical to running without `--bugs`, so
  it reads as though the flag was ignored. Had to check `--json`'s `annotatedBugs: []` to be sure.

### Addendum: it misses jobs on the counted config too

On push 2040769 (rev 426333f3208b), Treeherder has 5 test-macosx1500-aarch64/opt-mochitest-plain
jobs (retriggers), and `fx-tests task` shows each failing the test twice: `Mf8CzC_1SqyMzZUNnylyrw`,
`ZllatqmNQE6yhjwUXKitSw`, `fEBQUHT2SFWbxQhJfcmEgg`, `Wof_ByIDQm2QDYTsIA3Mbg`,
`OAZ4QoWMR3yVwQxvlESj6Q`. `fx-tests test --task-ids` lists only `Mf8CzC_1SqyMzZUNnylyrw`.
So at least 8 failing jobs (16 failing runs) are missing next to the 2 counted.
