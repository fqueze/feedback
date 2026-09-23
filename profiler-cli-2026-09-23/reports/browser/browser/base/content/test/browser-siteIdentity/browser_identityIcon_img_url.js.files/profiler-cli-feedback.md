## Markers that start inside a zoom are buried under long ones that started before it

- Question: "what ran on the parent main thread in the 5 ms before this console error?"
- Command: `profiler-cli zoom push 30.596,30.6015 --session S; profiler-cli thread markers --list --limit 0 --search '-name:WindowProc,...' --session S`
- Expected: the markers that start in the window: runnables, promise callbacks.
- Got: about 100 rows of long `IPCOut`/`IPCIn`/`AsyncShutdown blocker`/`ExtensionParent` intervals that started seconds earlier. They come first, ahead of the runnable I was looking for (`ResolveOrRejectRunnable MultiGetUntrustedModulesData`).
- Workaround: listing more `-name:` exclusions, plus `awk '$3 >= "t=30.597s"'` on the text output. That string comparison breaks as soon as the times use a different unit (ms vs s).
- Would help: a `--starting-in-range` option (or a default) for `--list` under a zoom, or a `--max-duration` shown next to `--list` in the help.

## (review) No way to ask "when, and from where, was this runnable queued?"

- Question: "was `dom::FlushRejections` at 30.599 s queued by the untrusted-modules rejection at 30.598 s, or by something earlier?"
- Command: `profiler-cli thread markers --search b204c51acecfe86f0 --list --limit 0 --session S` (the `task:` address of the runnable, used as the `flow=` of `TaskController::AddTask`)
- Expected: the `AddTask` that queued this runnable, with the runnable that was executing when it was queued.
- Got: 12 `AddTask`/`Runnable` rows over 3 s, because the address is reused by unrelated tasks. The pairing (AddTask 30.575 s -> FlushRejections 30.599 s, queued during an `unload`, not by the rejection) had to be inferred from row order, then from overlapping every other marker at 30.575 s.
- Would help: `marker info` on a `Runnable` showing the matching `AddTask` time and the enclosing runnable/marker at that time.

## (review) `marker info` with several handles changes the JSON shape

- Command: `profiler-cli marker info m-1 m-2 --json`
- Expected: a list of the same records a single-handle call returns.
- Got: `{"type": "marker-info-multi", "markers": [...]}`, so a one-liner written against the single-handle output printed `None`. Workaround: read `d['markers']`.
