## `thread markers --search` cannot match a number with a thousands separator

- Command: `profiler-cli thread markers --session S --search "id = 1,088" --list --limit 0`
- Expected: the DocShell marker whose label reads `DOCSHELL ... id = 1,088 about:newtab` (the label itself formats the number with a comma).
- Got: the comma is the OR separator, so it matched everything containing "id = 1" or "088" (100 KB of output).
- Workaround: searched by the docshell's address instead (`--search 7f4786956400`), which is ambiguous because addresses get reused over a 50-minute job.
- Suggestion: either do not insert thousands separators in labels built from log text (serial/id), or support quoting a term containing a comma.

## Unfiltered `--list --json` on a resource-usage profile times out

- Command: `profiler-cli thread markers --session S --list --limit 0 --json` on a 190,000-marker resource-usage profile (no zoom)
- Got: no output after 120 s (killed by the shell timeout). With `zoom push` to a 100 s window first, the same command returned in seconds.
- Question it was for: "which DOMWindow/DocShell markers of process X were alive when test Y started" — an interval-overlap query that `--search` cannot express.

## (review) Question: which marker does a link's `marker=N` point to?

- Command: `profiler-cli load '<url>/marker-table/?marker=181000&thread=0&v=17' --session S`, then `marker info --help`
- Expected: the load, or `marker info --index 181000`, names the marker that the link selects.
- Got: the load prints only the session status. No command takes a marker index, so checking each of a report's 20 links took a search per link and then `marker info --json` to compare `markerIndex`.
- Would help: `marker info --index N`, or print the selected marker when a loaded URL has `marker=`.
- Addendum to the thousands-separator entry above: `--search id:1088` (field:value with the raw number) finds the marker. That works around it better than searching by address.
