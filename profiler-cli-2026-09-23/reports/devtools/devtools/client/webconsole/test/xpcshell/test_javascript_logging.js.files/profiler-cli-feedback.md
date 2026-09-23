## Question: in which order did these events happen within the same second?

- Command: `profiler-cli thread markers --session <s> --search test_javascript_logging --list --limit 0`
- Expected: timestamps precise enough to order events that are milliseconds apart, e.g. `t=395.166s`.
- Got: once past one minute, times are rounded to whole seconds (`t=6m35s`). About 30 markers, from a parent, two children and the IO thread, all showed the same `t=6m35s`, so the sequence parent-teardown / child-send-failure / wait-start could not be read.
- Workaround: `--json` plus a script to print `start` in ms. The output could show milliseconds, e.g. `t=6m35.166s`.

## Question: which process and thread emitted each C++ warning?

- Command: the same `--list`.
- Expected: the `Process ID` / `Thread` fields of `cppDebug` markers in the row. That is the discriminator when parent and children log the same message.
- Got: only the message and the file:line. I needed `marker info` on each handle, or `--json`, to learn that `Call to Send() failed` came from child 21484's `IPC I/O Child`.
- Workaround: `--json` script printing `fields.pid` / `fields.thread`.

## Question: what was the machine's CPU use during this test?

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`, with a zoom on the test.
- Expected: the CPU percent per row, or a summary (min/avg/max) for the zoomed range.
- Got: 58 rows with only name, time and duration. `profiler-cli counter list` said "No counters in this profile". `profile info` said "No significant activity", although every CPU Use marker read 100%.
- Workaround: `--json` script over `data.cpuPercent`.
