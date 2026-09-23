## Question: in what order did these markers happen, when they fall within the same second? (browser_aboutSupport.js)

- Command: `profiler-cli thread markers --search GetKeySystemCapabilities,StartUtility --list --limit 0 --session S` (and `profile markers --search ...`) on a 2m25s profile.
- Expected: start times precise enough to order events a few ms apart (e.g. `t=141.697s` or `t=2m21.697s`).
- Got: `t=2m22s`, `t=2m24s` — whole seconds once the profile is over a minute long. The question here was whether a promise-rejection runnable ran before or after an IPC send 0.2 ms later, and whether a console error was the same event; seconds cannot answer that.
- Workaround: one `profiler-cli marker info m-N --json` per marker, reading `start` through a python one-liner — about 40 extra calls across two profiles.
- What the output could have shown: millisecond precision in `--list` rows (at least when the rows being listed share the same second), or a `--precise-times` flag.

## Default selected thread after load is not the parent main thread

- Command: `profiler-cli load <taskcluster per-test profile URL> --session S`
- Expected: t-0 (GeckoMain, Parent Process) selected, as the guide's workflow and the test log live there.
- Got: `Selected thread: t-211 (GeckoMain, Privileged Content)`.
- Workaround: `thread select t-0` before anything else.

## A failed load leaves its session behind, so retrying under the same id is refused (review of browser_aboutSupport.js)

- Command: `profiler-cli load <taskcluster per-test profile URL> --session S`, which failed with `Profile load failed: Could not fetch the profile on remote server. Response was: 503 Service Unavailable.` I then ran the same command again.
- Expected: the failed load cleans up after itself, or a retry under the same id replaces the dead session.
- Got: `Session S is already running. Stop it first or choose a different session id.` `session list` still showed S with a live daemon, and `status --session S` just printed the load error again.
- Workaround: `profiler-cli stop S`, then load again.

## `load` reports failure while the daemon goes on loading (review of browser_aboutSupport.js)

- Command: `profiler-cli load <profiler.firefox.com link to a 2m25s CI profile> --session S`
- Expected: `load` waits until the profile is loaded, or says it is loading in the background.
- Got: `Error: The profiler-cli daemon did not become ready within 500ms ...`, exit 1. The daemon was actually fine. The next `status` said `Profile still loading`, and a later one worked.
- Workaround: poll `status --session S` until it answers.
