## Question: in what order did these events happen, 30 ms apart?

- Command: `profiler-cli thread markers --search "OverLink,mousedown,mouseup,..." --list --limit 0 --session S` after `zoom push 225.5,229` (a 3.5 s view).
- Expected: start times at ms resolution in the list, since the question was whether a `mousedown` at 226.375 s came before or after an `OverLink` at 226.346 s and a timer at 227.428 s.
- Got: every row printed as `t=3m46s` / `t=3m47s`, so rows within the same second cannot be ordered or measured.
- Workaround: `--json | jq '.flatMarkers[] | .start/1000'`. The list could print ms (or relative-to-zoom) times when the view is a few seconds long.

## Question: the test's own log, without console-message stacks

- Command: `profiler-cli thread markers --category Test --search browser_opentabs_tab_indicators --list --limit 0 --session S`
- Expected: one line per TEST-PASS / INFO / TEST-UNEXPECTED-FAIL.
- Got: each `INFO Console message:` marker prints its full multi-line JS stack (40+ lines each), so the output was dominated by repeated FxA NO_ACCOUNT stacks.
- Workaround: redirect to a file, `grep -E '^  m-' | grep -v 'Console message'`. Truncating labels to one line in `--list` (full text via `marker info`) would answer it.

## Question: was this timer armed inside that 54 ms mousedown?

- Command: `profiler-cli marker stack m-1 --session S` (a `setTimeout callback` marker)
- Expected: the stack capture time at ms resolution, to compare with the `mousedown` it should fall inside.
- Got: `Captured at: 3m46s`.
- Workaround: `marker info m-1 --json`, then `stack.capturedAt`. Same fix as the `--list` times: print ms.
