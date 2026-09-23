## `load` fails in a sandbox where `~/.profiler-cli` cannot be created

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session <id>`
- Expected: the load to work, or to pick a writable directory by itself.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error does say to set `PROFILER_CLI_SESSION_DIR`, which fixed it; one wasted round trip. The brief does not mention the variable.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` and `profile-link.py` call.

## `load` selects a content process main thread, not the parent's

- Command: `profiler-cli load <per-test mochitest profile> --session <id>`
- Expected: t-0 (GeckoMain, Parent Process) selected, the thread where the test log is.
- Got: `Selected thread: t-46 (GeckoMain, Privileged Content)`, which has none of the Test markers.
- Workaround: `profiler-cli thread select t-0` straight after loading.

## `thread markers`/`profile markers --search` does not match the label it prints (review)

- Command: `profiler-cli profile markers --session <id> --search "keypress - "` (zoomed on the key presses)
- Expected: the `keypress - browser@158c70a00` and `keypress - body@10e908730` DOMEvent markers, whose printed label is exactly that text.
- Got: `No markers match the specified filters (searched 21 threads).` The label is put together from the `eventType` and target payload fields, and no single field holds `keypress - `. Searching for text copied from the output fails without saying why.
- Workaround: `--search keypress`.
