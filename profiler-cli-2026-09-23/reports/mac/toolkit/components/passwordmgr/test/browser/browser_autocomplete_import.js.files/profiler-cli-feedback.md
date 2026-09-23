## Which markers in a range were caused by a given function (search markers by a frame of their stack)

- Question: "which of the ~40 SetNeedStyleFlush markers between popupshowing and the click were set by `adjustHeight` (and from a FrameRequestCallback)?"
- Command: `profiler-cli thread markers --session bacimport-1 --search SetNeedStyleFlush --list --limit 0` inside a `zoom push 8.194,8.2`
- Expected: a way to filter or annotate markers by a frame in their captured stack (e.g. `--stack-search adjustHeight`, or the leaf JS frame printed in the `--list` row).
- Got: the list shows only name/time; the stack needs one `marker stack m-N` call per marker.
- Workaround: `--json` to get the handles, then a shell loop of `marker stack` per handle piped through `rg adjustHeight` (about 40 daemon calls per window, repeated for four windows).

## Default session directory not writable under the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_autocomplete_import.js-1`
- Expected: load.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` (the error message suggested it). Also shortened the session name, since the socket path has to stay short.

## The raw artifact URL behind a session loaded from a profiler link (review-browser_autocomplete_import.js)

- Question: "which Taskcluster artifact is this session showing, so that I can link a new observation from it?"
- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/<encoded taskcluster url>/marker-chart/?range=…' --session review-bacimport-1`, which is how the review brief says to check a report's links, then `profile-link.py --session review-bacimport-1 --marker m-1367`.
- Expected: a link.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stopped the session and loaded the raw Taskcluster URL into a new one, which meant downloading each profile again and finding the markers again, since the handles change. That is two extra loads for three links.
- Could have: `profiler-cli status --json` giving the decoded `from-url` artifact URL of a session loaded from a profiler link, so that link builders can use it.
