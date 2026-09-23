## Question: "in which order did these markers happen?" (sub-millisecond ordering in `thread markers --list`)

- Command: `profiler-cli zoom push 1.590,1.682 --session <s>` then `profiler-cli thread markers --session <s> --list --limit 0 --search "-name:Preference Read,..."`
- Expected: start times precise enough to order markers inside a 92 ms view.
- Got: every marker in the key 5 ms window printed as `t=1.647s` (the connection close, the setTimeout runnable, NotifyUnhandledRejections), so the order that decides the race could not be read.
- Workaround: `profiler-cli marker info m-247 m-314 ... --json` and a Python one-liner printing `start`/`end`. The `--list` output could scale its time precision to the view range (e.g. `t=1646.566ms` when zoomed under a second).
## Question: "in which order did these markers happen?" — hit again while reviewing (review-test_NimbusTestUtils.js)

- Command: `profiler-cli zoom push 1.636,1.653 --session review-test_NimbusTestUtils.js-1` then `profiler-cli thread markers --session review-test_NimbusTestUtils.js-1 --list --limit 0 --search '-name:Preference Read'`
- Expected / got: same as the entry above. In a 17 ms view, every row printed `t=1.647s`.
- Workaround: `marker info <handles> --json` piped into a Python one-liner.
