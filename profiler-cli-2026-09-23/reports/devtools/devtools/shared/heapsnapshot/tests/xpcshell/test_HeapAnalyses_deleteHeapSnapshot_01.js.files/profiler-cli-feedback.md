## test_HeapAnalyses_deleteHeapSnapshot_01.js (2026-09-22)

- **Question: in what order, to the millisecond, did these harness lines happen?** The `ps` behind
  the failure message is 1 ms before it, and the process names 100 ms apart decide the mechanism.
  `profiler-cli thread markers --session <s> --search deleteHeapSnapshot_01 --list --limit 0` prints
  `t=3m10s`; `profiler-cli marker info m-3 --session <s>` prints `Time: 3m10s (instant)`. Expected:
  ms, e.g. `t=190.138s`, at least in `marker info` and when adjacent rows share the same second.
  Workaround: `--json` and `start`.
- **Question: which app processes were on the device at time t, and which pid changed name?** Same
  as the sibling report: the `get_process_list` DEBUG payload is cut before the app's
  `…test_runner:xpcshellN` entries. Command:
  `profiler-cli thread markers --session <s> --search "name:INFO,name:DEBUG,name:test" --list --limit 0 --json`
  (100 MB) plus a script. An `--extract <regex>` over payloads would have answered it.
