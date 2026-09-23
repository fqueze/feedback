## `marker info` rounds times to whole seconds in long profiles

- Command: `profiler-cli marker info m-650 m-651 m-653 m-475 --session <s>` on a 55-minute resource-usage profile.
- Expected: enough precision to tell which of two subtests (both logged at "6m55s") a DOMWindow marker was created in.
- Got: every marker printed as `Time: 6m55s`; the `--list` output rounds the same way.
- Workaround: `marker info ... --json` and read `start` (ms). The JSON records also have `handle: null`, so the handle had to be matched by order.
