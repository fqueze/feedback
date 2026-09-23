# profiler-cli feedback (browser_storage_delete_all.js)

## The session opened on a content-process thread, and a filtered marker query quietly returned nothing

- Command: `profiler-cli load <N9Bqcq2qSU63odXx9M0cbQ .../profile_browser_storage_delete_all.js.json> --session browser_storage_delete_all.js-2`, then `profiler-cli thread markers --session ... --category Test --search browser_storage_delete_all --list --limit 0`
- Expected: the session to start on the parent process GeckoMain (t-0), as it did for the Windows profile of the same test.
- Got: it started on `t-69 (GeckoMain, Privileged Content)`, and the query returned "0 markers (filtered from 26110)". Nothing said that the test markers live on another thread.
- Workaround: `thread select t-0`.
- Suggestion: start on the parent process main thread. Or, when a `--category`/`--search` query finds nothing on the selected thread, say which threads do have matches (the way `profile markers --search` does).

## Question: "did request B start before request A had finished?"

- Command: `profiler-cli thread markers --search "RDP,..." --list --limit 0`
- The text list rounds start times to 1 ms and prints a duration but no end time. To find out whether `localStorage:getStoreObjects()` began before `localStorage:getFields()` had returned (a gap of about 0.5 ms), I had to run a script over `--json` and compute `start + duration`.
- What would have answered it: an option to print start and end with sub-millisecond precision in `--list`, e.g. `--precise` or `--columns start,end`.

## Question: "which markers start inside marker X?" (here: which debounce timer runnables sent a getStoreObjects request)

- Command: the same `--json` list, then a script that matches each `setTimeout() for exports.debounce...` Runnable against the `RDP Front ...getStoreObjects()` markers starting inside it, across the whole 43 s profile.
- What would have answered it: a nesting filter on `thread markers`, e.g. `--within-marker --search <text>` (the marker counterpart of `filter push --during-marker`), or `--group-by enclosing:<name>`.

## Session directory not writable under the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggested `PROFILER_CLI_SESSION_DIR`, and that fixed it.
- Cost: one failed load. The brief's command templates don't mention the variable, so every agent pays this once.

## (review) A row's label text is not searchable, and nothing says so

- Command: `profiler-cli thread markers --search "debounce,getStoreObjects,resize - window,Toolbox opened and focused" --list --limit 0`
- Expected: the `DOMEvent` rows that the list prints as `resize - window`.
- Got: those rows were silently missing. The other terms matched. `resize - window` is the printed label, name plus target, and not a field value.
- Workaround: `--search eventType:resize`.
- Suggestion: match the printed label too, or warn when a term contains " - " and matches nothing.

## (review) Question: "which window is innerWindowID 418?"

- Command: `profiler-cli thread markers --search "innerWindowID:418" --list`, after `marker info m-11 --json` showed `rawFields: innerWindowID 418` on a `resize - window` event.
- Got: 0 markers. Raw fields are not searchable, and nothing maps the ID to a URL.
- What would have answered it: a URL or page title next to the innerWindowID in `marker info`, taken from the profile's pages table. Or a `profile pages` command.
