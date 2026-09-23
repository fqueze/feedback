# profiler-cli feedback — browser_autocomplete_secondary_actions.js

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_autocomplete_secondary_actions.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by an "Unknown session" error from the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it, which saved time). `profile-link.py` honoured the same variable.
- Cost: one failed load. The subagent brief does not mention the variable, so every agent in a sandbox will hit this once.

## (review) profile-link.py refuses a session loaded the way the review brief says to load it

- Command: `profiler-cli load '<profiler.firefox.com/from-url/... link from the report>' --session review-...-3`, as `review-brief.md` prescribes, then `python3 profile-link.py --session review-...-3 --marker m-303`.
- Expected: a link to the new marker the review found on the same thread.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: none cheap; a second 0.6 GB load of the raw Taskcluster URL just to link one marker. The loaded link's own thread and URL were already known to be good.
- What would have answered it: `profile-link.py` extracting the `from-url` artifact URL (and keeping the link's `thread=`) when the session was loaded from a profiler.firefox.com/from-url link. `profiler-cli` loads that form without complaint, so the two tools disagree.
- Also recurred for the review: the `PROFILER_CLI_SESSION_DIR` sandbox issue logged above; `review-brief.md` does not mention the variable either.
