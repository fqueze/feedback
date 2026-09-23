# profiler-cli feedback from test_bug599295.html

## `profile markers --search <hostname>` dumps the mochitest PAC script 128 times

- Command: `profiler-cli profile markers --session b599295-1 --search redirproxy`
- Expected: the network and channel markers for `https://redirproxy.example.com/test`.
- Got: 244.7 KB of output: 126 `Preference Read` markers for `network.proxy.autoconfig_url`, each
  printed with the whole PAC script (which lists every mochitest host), before the 2 markers
  wanted. Any test-server hostname search hits this in a mochitest profile.
- Workaround: `--search "redirproxy,-name:Preference Read"`.
- Suggestion: cut long payload values in the one-line list (the full value is in `marker info`).

## Default thread after `load` is not the test's process

- Command: `profiler-cli load <.../profile_test_bug599295-2.html.json> --session b599295-2`
- Expected: the `http://mochi.test` main thread selected, as for the first profile of the same test.
- Got: `t-8 (GeckoMain, Privileged Content)` selected (the highest-CPU content process), so
  `thread markers --category Test` answered "No markers match", which reads like the test logged
  nothing.
- Workaround: `thread list`, then `--thread t-6`.
