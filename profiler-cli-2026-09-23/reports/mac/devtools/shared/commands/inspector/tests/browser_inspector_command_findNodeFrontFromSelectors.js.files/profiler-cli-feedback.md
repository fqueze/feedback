## Default session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "<taskcluster profile URL>" --session <name>`
- Expected: the profile loads.
- Got: `Error: Cannot create the profiler-cli session directory /Users/florian/.profiler-cli. ... EPERM`
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (the error message suggested it; other agents already use that directory). `profile-link.py` reads the same variable, so it has to be set there too. One wasted call; the message was good.
- Related: with that directory, the brief's session name `<report name>-1` (57 characters for this report) gives a socket path of about 103 bytes, right at the macOS `sun_path` limit. I used a shorter name (`findNodeFrontFromSelectors-1`) instead of testing whether the long one works.

## Question: which document is this DOMWindowCreated / JSActor message about?

- Command: `profiler-cli marker info m-403 m-405 m-401 --session findNodeFrontFromSelectors-1`
- Expected: the document URL, or at least "top-level" versus "iframe", for each `DOMWindowCreated` marker.
- Got: only `innerWindowID: 21474836483` under "Other payload fields (no schema)". I had to find a Network marker with `innerWindowID: 21474836481` (the iframes' loads name the top window) to tell which of the three windows was the test page. The iframes' windows stayed identified only by their order and the `beforeunload` right after each one.
- Could have shown: the profile's `pages` table maps innerWindowID to URL, embedder and whether it is a subframe. Resolving `innerWindowID` against that table in `marker info` (and as a column in `thread markers --list`) would have answered this directly.

## `marker info --json` returns a different shape for one handle than for several (review-browser_inspector_command_findNodeFrontFromSelectors.js)

- Command: `profiler-cli marker info m-15 --json --session rv-fnfs-2`, then `profiler-cli marker info m-307 m-298 --json --session rv-fnfs-1`
- Expected: the same top-level shape either way, so one script reads both.
- Got: one handle returns the marker object itself (`markerIndex` at the top level). Several handles return `{type, requested, markers: [...], errors, context}`. A script written for one shape got `KeyError` on the other, twice.
- Workaround: `d.get('markers', [d])`.
- Question behind it: "what `markerIndex` does this handle have?", to check a report's `marker=N` links as the review brief asks. A `markerIndex` column in `thread markers --list`, or `markerIndex` in the plain-text `marker info`, would answer it without JSON.
