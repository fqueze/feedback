## `fx-tests test <path> --bugs` prints no bugs section at all when it finds none

- Command: `COLUMNS=250 fx-tests test devtools/shared/transport/tests/xpcshell/test_bulk_error.js --bugs`
- Expected: a "Bugs" section, saying "none found" if empty, and ideally the bugs its failing jobs were starred on (here XfKT04wZR42kSNwV6wuRpQ is starred on bug 1991833, whose summary names no test).
- Got: the same output as without `--bugs`, with no line mentioning bugs, so I could not tell "searched, none" from "flag ignored".
- Workaround: found the bug from a sibling report, then confirmed with `fx-tests intermittent --bug 1991833 --since 21 --limit 0`.

## `fx-tests intermittent --bug <N>` defaults to 7 days while `fx-tests test` covers 21

- Command: `fx-tests intermittent --bug 1991833`
- Expected: the occurrence list to cover the failure `fx-tests test` reported (2026-09-10).
- Got: 10 occurrences from 2026-09-16 on, with no hint that the window was narrower than the test's data. The failing task only showed with `--since 21`.
- Workaround: `--since 21 --limit 0`.
