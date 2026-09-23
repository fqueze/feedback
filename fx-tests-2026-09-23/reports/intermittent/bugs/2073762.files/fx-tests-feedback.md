## Question: every job a sheriff starred on one bug, on all trees

- Command: `fx-tests intermittent --bug 2073762 --limit 0`.
- Expected: the bug's annotations. Bug 2073762 comment 6 and 7 say mozilla-beta is hit as often as trunk.
- Got: "21 sheriff annotations on trunk". Treeherder `failuresbybug` has 45: 19 autoland, 2 mozilla-central, 21 mozilla-beta, 1 mozilla-esr153, 2 try. Nothing says how many were left out.
- Workaround: `curl 'https://treeherder.mozilla.org/api/failuresbybug/?startday=2026-09-14&endday=2026-09-23&tree=all&bug=2073762'`.
- What could have shown it: a count of the off-trunk annotations next to "on trunk", or a `--tree all` option.
