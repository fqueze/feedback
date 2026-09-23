## `--bugs` finds no bug for a test whose tracking bug names it

- Command: `fx-tests test devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js --bugs` (and `--json`: `"annotatedBugs": []`).
- Expected: bug 2069100 ("Intermittent devtools/client/inspector/grids/test/browser_grids_grid-list-on-iframe-reloaded.js | single tracking bug"), which `fx-tests intermittent --bug 2069100 --since 21` shows with 15 sheriff annotations in the window.
- Got: no bug section at all in the text output, and an empty list in JSON.
- Workaround: `fx-tests intermittent --bug <N> --since 21`, with the bug number taken from the brief.

## Question: "on which days did each failure mode occur, and when did one stop?"

- Command: `fx-tests test <path> --history` gives per-day totals only; I had to loop `fx-tests test <path> --day <d>` over a dozen days and read the Issues block of each.
- What would have answered it: `--history --issue <n>`, or a per-issue sparkline in the Issues block. Here, the bug's own mode (`getFragments` rejection) stopped the day its fix landed while another mode ("The checkbox is checked") kept going, and the day totals alone hide that.

## Issues counts differ with and without `--config`

- `fx-tests test <path> --task-ids --issue 2 --limit 0` puts 71 "The checkbox is checked" failures on windows11-64-25h2-asan; `fx-tests test <path> --config windows11-64-25h2-asan` lists 68 for the same message, plus 12 "Failure details not recorded (likely Android or platform logging issue)", a row that never shows up in the unfiltered Issues block (whose rows add up to all 413 failures).
- Expected: the same attribution either way, or a note about why they differ.
