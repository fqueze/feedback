## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` (and the same for the Test category, IPC, RefreshDriverTick) on a 3-minute profile.
- Expected: start times precise enough to order markers within one second (the failure here is a 15 ms race between a parent reflow and a synthesized click).
- Got: every row printed `t=2m33s`; all ~70 DOMEvents of interest shared that one value, so the list could not say whether the mousedown came before or after `PBrowser::Msg_UpdateDimensions`.
- Workaround: `--json` piped into a python one-liner printing `start` with 2 decimals, for every list I read (about 10 times). The text output could show milliseconds (e.g. `t=152766.11ms` or `2m32.766s`) when the view is longer than a few seconds, or always in `--list` mode.
