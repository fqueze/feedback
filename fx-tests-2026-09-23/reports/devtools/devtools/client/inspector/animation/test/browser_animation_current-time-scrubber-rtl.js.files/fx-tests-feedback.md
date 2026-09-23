## `test --bugs` says nothing when it finds no bug

- Command: `COLUMNS=250 fx-tests test devtools/client/inspector/animation/test/browser_animation_current-time-scrubber-rtl.js --bugs`
- Expected: a "Bugs" section, and when empty a line such as "No bug names this test" (and ideally closed ones too, e.g. old resolved intermittent bugs).
- Got: the same output as without `--bugs`, with no bug section at all, so it is unclear whether it searched and found nothing or the flag was ignored.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=current-time-scrubber&include_fields=...'`, which found only bugs resolved in 2018–2020.
