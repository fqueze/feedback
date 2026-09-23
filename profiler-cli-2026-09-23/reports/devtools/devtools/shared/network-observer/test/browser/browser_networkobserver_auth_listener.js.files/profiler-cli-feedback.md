## `--search` does not match the text `thread markers --list` prints for a `test` marker

- Command: `profiler-cli thread markers --search 'PASS — devtools/shared/network-observer/test/browser/browser_networkobserver_auth_listener.js' --list`
- Expected: the `test` marker printed as `PASS — devtools/.../browser_networkobserver_auth_listener.js`.
- Got: no match; the printed line joins two fields with an em dash, so the search has to be on one of them.
- Workaround: `--search name:test` and grep. Minor, one extra call.
