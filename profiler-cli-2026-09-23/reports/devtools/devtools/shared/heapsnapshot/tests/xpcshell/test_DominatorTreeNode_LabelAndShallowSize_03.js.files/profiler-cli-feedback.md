## Question: which device process slot each test used, and which processes existed over time

- Needed for an Android xpcshell job's resource-usage profile: map each test to its `XpcshellTestRunnerService$iN` slot (from `launch_application: am startservice` INFO text), and track which `org.mozilla.geckoview.test_runner:xpcshellN` pids exist over time (from the `get_process_list:` DEBUG text).
- Commands: `profiler-cli thread markers --search "am startservice" --list --limit 0 --json` and `--search get_process_list --list --limit 0 --json`, then a Python script with regexes over `label`.
- The default output cut each of those markers to a single line that began with hundreds of irrelevant kernel processes, so neither the slot nor the test_runner processes were visible. A way to grep inside a marker's text and print only the matching part (e.g. `--search` with a displayed match excerpt) would have answered it without a script.

## Question (review): which name did each pid have across snapshots, and when did it change?

- Needed to tell a stray `:xpcshell0` process from a neighbour that was still starting and was listed under the wrong name for a moment. This needs the `org.mozilla.geckoview.test_runner*` entries of every `get_process_list:` DEBUG marker, per pid, over time.
- Commands: `profiler-cli thread markers --search get_process_list,startservice --list --limit 0 --json` (68-76 MB per profile), then a Python script with regexes over `label`.
- Same problem the author logged: each label begins with hundreds of kernel processes, so the default output never shows the part that matters. Showing the part of the label that matched the search (e.g. `--search 'test_runner'` printing only the matching fragments) would answer it without a script.
