## `thread samples` truncates function names so the distinguishing part is lost

- Command: `profiler-cli thread samples --session browser-test_dynamicLauncher.js-4` (zoomed on a long task)
- Expected: the test function names in the stack, e.g. `test_dynamicLauncher.js!expectDbusMockCall`, `test_dynamicLauncher.js!test_install_error`.
- Got: `/builds/worker/workspace/build/tests/xpcshell/tests/browser/components/shell/test/unit/test_dynamicLauncher.js!expect...` and `...!test_i...` — the long CI path prefix eats the width and the function name, the only useful part, is cut.
- Workaround: `function expand f-N`, one call per frame. Truncating the path (middle) rather than the function name would avoid it.

## Question: "how busy was the machine over this range?" in a resource-usage profile

- Command: `profiler-cli thread markers --session <resource-usage session> --search "name:CPU Use" --group-by field:idle_pct` after `zoom push 122,242`
- Expected: a summary of the machine's CPU over the range (min/median/max of `cpuPercent`, share of time saturated).
- Got: one histogram bucket per distinct formatted percentage (dozens of rows like `1.2%: 100 markers`), and `counter list` says "No counters in this profile" because CPU is stored as `CPU Use` markers, not counters.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped into a Python script computing median and the fraction >= 95%. A numeric field summary (min/median/p95/max) for `--group-by field:X` or a `CPU Use` roll-up in `profile info` for resource-usage profiles would answer it directly.

## Question: "which function dominates each of these N long markers?"

- Command: `thread markers --search name:MessageEventToParentRunnable --min-duration 50 --list` gave 9 long tasks; answering what each one ran needed `zoom push m-N` + `thread samples --json` + a script, per marker.
- Expected: a way to see the top self/total function per listed marker (e.g. `--with-top-function`), or `thread samples --during-marker m-N`.
- Workaround: loop in the shell over `zoom push` / `thread samples --json` / `zoom pop`.

## Default selected thread after `load` is not GeckoMain on some xpcshell profiles

- Command: `profiler-cli load <TbiFDu3bQrO1_l7IDkRFSA profile_test_dynamicLauncher.js.json> --session ...-2`, then `thread markers --category Test --list --limit 0`
- Expected: the parent GeckoMain selected, as on the other xpcshell profile (whose `load` selected t-0 GeckoMain).
- Got: t-1 (DOM Worker) was selected, since it had more CPU; the Test query returned "No markers match", which reads like a missing test log. `profile info` also showed "Name: Untitled profile / Platform: Unknown" for this profile.
- Workaround: `thread select t-0` explicitly.
