## `--issue <n>` numbers change meaning when `--config` is added

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_amp.js --task-ids --limit 0 --issue 1 --config test-linux2404-64-tsan/opt-xpcshell`
- Expected: the tsan tasks of issue 1 as numbered without a filter ("TIMEOUT Test exceeded time limit").
- Got: tsan tasks whose failure is the unrecorded-details FAIL (a TSan thread leak), apparently because
  issues are re-ranked within the filtered config, where that FAIL is the top one. Nothing in the output
  says which message "issue 1" now is.
- Workaround: checked each task with `fx-tests task`. Printing the issue's message next to the task list,
  or keeping the unfiltered numbering, would avoid it.

## `--durations` on a config where the parallel run always fails shows the sequential retry's time

- Command: `fx-tests test <path> --durations --limit 0`
- Got: `test-linux2404-64-artifact/debug-xpcshell` median 15,964 ms, among the fastest configs, while
  it is the worst config (50 %, times out at 30 s in every parallel run). Its passing runs are all
  harness reruns, in the sequential phase, on an idle machine.
- Also: passing medians above the timeout (`test-linux2404-64/debug-xpcshell` 64,390 ms with a 60 s
  timeout; `macosx1500-aarch64-vms/debug` 32,799 ms with 30 s), since the duration includes process
  startup and shutdown and the harness timer does not.
- Would help: split durations by execution mode (parallel / sequential), and print the config's timeout
  (base 30 s × `--timeout-factor` × `requesttimeoutfactor`) next to them.
