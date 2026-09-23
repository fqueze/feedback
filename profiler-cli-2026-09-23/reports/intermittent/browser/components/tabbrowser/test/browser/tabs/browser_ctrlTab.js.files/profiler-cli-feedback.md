## Question: in which order, and how far apart, did markers within the same millisecond happen?

- Command: `profiler-cli thread markers --search "DOMEvent,thumb,Worker,page-thumbnail" --list --limit 0 --session …`
- Question: whether an image `error` event came before or after a worker reply 0.06 ms later, and the gaps between each store, load and write reply (all within 7.634–7.640s).
- Got: every row shows `t=7.636s`: the list order holds, but the gaps do not, and noise names (Histogram::Add, Preference Read, WindowProc) could not be dropped without also listing every excluded name as `-name:`.
- Workaround: `--json` and a Python filter printing `start` to 3 decimals of ms.
- Would have helped: printing times with sub-ms precision when neighbouring rows share the same millisecond (or a `--precise` flag).
