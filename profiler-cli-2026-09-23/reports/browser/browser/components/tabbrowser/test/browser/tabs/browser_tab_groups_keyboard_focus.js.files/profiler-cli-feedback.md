## WindowProc `lParam` is printed with a thousands separator, so it looks like an "x,y" pair

- Command: `profiler-cli marker info m-898 --session …` (a `WM_NCHITTEST` WindowProc marker)
- Expected: a raw integer, or hex, for a packed Win32 `lParam`. Better still for mouse messages (`WM_*MOUSE*`, `WM_NCHITTEST`): x = LOWORD, y = HIWORD.
- Got: `lParam: 656,360`. It reads as x=656, y=360, but it is 656360 = 0x000A03E8, i.e. x=1000, y=10. I built part of an explanation on the wrong reading before I caught it.
- Workaround: decode it by hand. The grouping may come from the schema's `Format::Integer`, not from profiler-cli.

## (review) The payload fields of listed markers: which hit-test code each WM_SETCURSOR carried, in a range

- Question: for every `WindowProc` marker from 1 s to 3.1 s, its `uMsg`, `wParam` and `lParam` — to see what each `WM_NCHITTEST` answered (the `WM_SETCURSOR` right after it carries the hit-test code in `LOWORD(lParam)` and the hwnd in `wParam`).
- Command: `profiler-cli thread markers --search WindowProc --list --limit 0 --session …`
- Got: one row per marker with only the name (`nsWindow - WM_SETCURSOR (32)`); the fields need `marker info` on each handle. I wrote a script over `--json` `flatMarkers[].fields` instead.
- Could have shown: an option to print chosen payload fields as columns in `--list` (e.g. `--fields wParam,lParam`).
