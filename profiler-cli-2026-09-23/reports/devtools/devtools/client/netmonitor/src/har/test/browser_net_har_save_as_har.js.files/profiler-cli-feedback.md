## Default thread after `load` is not the parent process main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/W03A_t-dSfyMOKxH5Bnc7A/runs/0/artifacts/public/test_info/profile_browser_net_har_save_as_har.js.json --session browser_net_har_save_as_har.js-2`, then `thread markers --category Test --search browser_net_har_save_as_har --list --limit 0`
- Expected: the parent process GeckoMain (t-0) selected after load, as with the macOS profile of the same test.
- Got: `t-13 (GeckoMain, WebExtensions)` selected; the Test-marker query returned "0 markers", which reads like "the test logged nothing".
- Workaround: `thread select t-0` after every load.

## `profile markers --search` prints multi-KB payload values in full

- Command: `profiler-cli profile markers --session browser_net_har_save_as_har.js-1 --search "example.com"`
- Expected: one line per marker, with long field values cut.
- Got: each `Preference Read` marker for `network.proxy.autoconfig_url` printed the whole PAC script (several KB per line) before any useful row.
- Workaround: search in `thread markers` with `--category Network` instead.
