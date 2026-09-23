## Exact time of one marker in a long profile

- Question: at what time (to the ms) did the FAIL marker of this test fire, in a 34-minute resource-usage profile?
- Command: `profiler-cli thread markers --session <s> --search treeWidget_keyboard --list --limit 0`
- Expected: a timestamp precise enough to tell markers of one test apart (they are ms apart).
- Got: `t=25m8s` / `t=25m9s` for every marker of the test: whole seconds only, so the order and gaps within the 1.7 s test cannot be read.
- Workaround: `profiler-cli marker info m-12 --json` and read `start` (1508863.202 ms).
- Could have shown: `t=25m8.863s`, i.e. ms precision once the profile is longer than a minute.
