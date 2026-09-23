## `errors --message` misses a multi-line console.error

- Command: `fx-tests errors --day 2026-09-16 --message "remote browser crashed"` (also `"crashed while on"`, `"remote browser"`)
- Expected: at least task OS7C65E0QmOR6EjrEZO-BA (browser_net_decode-url.js), whose log has
  `console.error:` / `  remote browser crashed while on` / `  about:blank` on three lines.
- Got: "No markers matched." The resource-usage profile shows why: the `console.error` marker has
  an empty text and the message sits in two separate `output` markers.
- Workaround: none within fx-tests; read the job log. The question was "which other jobs had a tab
  crash on about:blank that day", which this output could have answered.

## Did the try push run any config the removed skip-if covered?

- Question: the removed `skip-if` was `linux 24.04 x11 debug && http3`; did the push run an http3
  job at all?
- Command: `fx-tests try 2888bcab0070 --test <path> --all-jobs` lists only the configs where the
  test ran, so an http3 config that was never scheduled is just absent.
- Workaround: a script over the Treeherder jobs API listing job names containing `http3` (none).
- What the output could show: for `--test`, the test's skip-if conditions from the manifest and
  whether any job of the push matched each one.
