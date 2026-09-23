## Question: "how many times did each message repeat in this test, and from when to when" (length of a polling loop)
- Command: `profiler-cli thread markers --session <s> --search docshell/test/navigation --list --limit 0`
- Expected: a grouped view with count, first time and last time per distinct marker text, e.g. `--group-by name,field:... --with-range`.
- Got: 633 identical `Reason: Polling` rows in the list, hiding the few meaningful ones. The aggregate view gives counts per marker name, not per message, and no first/last times.
- Workaround: `--list --json` and a Python script, then `grep -v` on the text output to see the other markers.
