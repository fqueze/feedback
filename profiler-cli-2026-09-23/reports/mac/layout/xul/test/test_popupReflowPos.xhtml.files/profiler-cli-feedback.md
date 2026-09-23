## Field search does not match payload fields without a schema

- Command: `profiler-cli thread markers --session test_popupReflowPos.xhtml-1 --search 'innerWindowID:32' --list --limit 0`
- Expected: the markers whose payload has `innerWindowID: 32` (`marker info` shows it under "Other payload fields (no schema)" on DOMEvent, RefreshDriverTick, Reflow and rAF markers).
- Got: 0 markers.
- Workaround: `--json` and a Python filter on `data.innerWindowID`.

## Question: which document / refresh driver does each tick and rAF marker belong to?

- Command: `profiler-cli thread markers --search 'requestAnimationFrame,RefreshDriverTick' --list --limit 0`
- Needed to tell apart two refresh drivers ticking in the same vsync (harness tab vs browser window), and which one ran a given `requestAnimationFrame callbacks` marker. The list shows neither the `innerWindowID` nor sub-millisecond times (`t=3.441s` for three markers whose order decides the diagnosis).
- Workaround: `--json`, printing `start`/`end` in ms and `data.innerWindowID`.
- What would have answered it: an innerWindowID (or page URL) column in `--list`, and times with enough digits to order markers that start in the same millisecond.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session test_popupReflowPos.xhtml-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (a wrapper script, since zsh does not word-split a variable holding the env prefix). Minor; the hint made it quick.
