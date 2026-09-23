## Question: when exactly did each window of one process start and end, next to the test boundaries?

- Command: `profiler-cli thread markers --session <s> --search "name:DOMWindow,name:DocShell" --list --limit 0` (and `--search pid:7372`)
- Expected: start and end times precise enough to order a `--DOMWINDOW` against a `test` marker's end, which were 0.5 s apart here.
- Got: past one minute, `t=` is rounded to whole seconds (`t=3m32s`), the end time is not shown, and the duration is rounded (`7.346s`). You cannot tell whether a window died before or after `test_end`.
- Workaround: `--list --limit 0 --json` plus a Python script to compute `start`/`start+duration` in ms per pid. `marker info --json` gives `start`/`end` one marker at a time.
- What would have answered it: an option for ms precision on `t=` (or showing the end), for example `--precise-times`.

## `field:value` search is a substring match on numeric fields

- Command: `profiler-cli thread markers --session <s> --search "serial:35,serial:36" --list`
- Expected: windows with serial 35 or 36.
- Got: also serial 135, 136, 235, 335, 350–362 (141 markers).
- Workaround: the JSON and a script. An exact-match form (`serial:=35`) would help.

## (review) A pid copied from a marker label is split on its comma

- Command: `profiler-cli thread markers --session <s> --search "7,372" --list --limit 0 --json`
- Expected: markers of process 7372; the labels print pids as `[Child 7,372: …]`, so that is what you copy.
- Got: 31,188 of 81,072 markers, since `,` is the OR separator ("7" or "372").
- Workaround: `pid:7372` or a script over `data.pid`.
- What would have answered it: printing pids without a thousands separator in labels, or a warning when a term is all digits after splitting on a comma inside a number.

## (review) Question: which objects died after their process's `Completed ShutdownLeaks collections` line?

- Command: `thread markers --search "name:DOMWindow,name:DocShell,Completed ShutdownLeaks" --list --limit 0 --json`, then a script joining each object's end time with its pid's collection time.
- What its output could have shown: nothing in the default output relates an interval marker's end to another marker per pid; that join had to be scripted.
