## Question: the exact time and order of log lines within one second (long profile)

- Command: `profiler-cli thread markers --search test_create_profile --list --limit 0 --session <s>` on a 28-minute resource-usage profile (JUfa5PoLQumisksWau_HjA).
- Expected: times precise enough to order and space the shutdown log lines (they are 0.1-0.5 s apart).
- Got: every row printed as `t=9m32s` / `t=9m33s`, so `exiting test`, two C++ warnings, the SVGDocumentWrapper warning and the assertion all read as the same instant.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 3 decimals. The default list could print milliseconds once the profile is longer than a minute (e.g. `9m32.479s`), or relative to the zoom start.

## `✓ has stack trace` on markers whose stack is empty

- Command: `profiler-cli marker info m-12 m-13 m-44 --session <s>` (cppDebug "C++ warning" markers in a resource-usage profile).
- Expected: either a stack, or no `✓` in the list.
- Got: the list shows `✓`, and `marker info` prints `Stack trace: [1] unknown!null`.
- Workaround: none needed, but it cost a round-trip. Printing "no stack recorded" (or no `✓`) would save it.
