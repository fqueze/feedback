# profiler-cli feedback — browser_toolbox_options_local_mode.js (2026-09-22)

## The order and millisecond timing of markers within one second of a long profile
- Command: `profiler-cli thread markers --category Test --search browser_toolbox_options_local_mode --list --limit 0 --session <s>` on an 8-minute profile.
- Expected: start times precise enough to order events (ms), as the guide's `t=12.671s` examples suggest.
- Got: `t=7m2s` for every marker in a 2-second stretch holding the whole failing sequence; `marker info` also prints `Time: 7m2s`.
- Workaround: a jq script over `--list --json` printing `.start/1000` with 3 decimals (`ml.sh` in this directory). The default output could print seconds with ms (`421.868s`) whatever the profile length.

## Flat-list JSON has `duration`, `marker info --json` has `end`
- Command: `thread markers --list --json` vs `marker info m-N --json`.
- Expected: the same field names.
- Got: `.flatMarkers[]` has `start`+`duration`, no `end`; `marker info` has `start`+`end`+`duration`. My script read `.end` and printed every interval marker as instant, which briefly hid that `tabDescriptor:navigateTo()` took 1.2 s.

## The default selected thread after `load` is not the parent main thread
- Command: `profiler-cli load <.../profile_browser_toolbox_options_local_mode-2.js.json> --session <s>`, then `thread markers --category Test --search ... --list`.
- Expected: GeckoMain of the parent process selected, as in the other profiles of the same job.
- Got: `t-15 (GeckoMain, WebExtensions)` selected; the Test-marker query returned nothing, with no hint that it ran on the WebExtensions thread.
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.

## The reason and zones of a GCMajor
- Command: `profiler-cli marker info m-1430 --json`.
- Expected: `reason`, `zones_collected`, `total_zones` as fields.
- Got: `fields` empty, `rawFields[].value` for `timings` is a JSON string, so `jq .timings.reason` fails; the text output truncates it after `minor_gc_number` with `…`.
- Workaround: grep the text output for `"reason":"..."`.
