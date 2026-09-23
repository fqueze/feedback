## Question: how far apart are two markers that print with the same millisecond?

- Command: `profiler-cli marker info m-96 m-171 m-523 m-388 --session ident-1`
- The race here was 0.6 ms wide: an idle task at 48412.14 against an activity runnable at 48412.77. `thread markers --list` and `marker info` print `t=48.412s` / `t=48.413s`, which gives the order (the list is chronological) but not the gap.
- Workaround: `marker info ... --json` and a Python one-liner to print `start`/`end`.
- What would have answered it: `marker info` printing its start and end at µs resolution (it already prints durations as `29.791μs`), or a `--precise` flag on `--list`.

## `profile markers --search` on a large debug profile ran for over two minutes

- Command: `profiler-cli profile markers --session ident-2 --search "identity@mozilla.org, api_event: webRequest.onBeforeRequest"` on `ZdqjMucGRL6tW3XbMPz2bQ`'s `profile_test_ext_protocolHandlers.html.json` (6 min, 198 threads, debug)
- Expected: a result within the 120 s tool timeout.
- Got: it ran for more than 120 s (finished later in the background) and printed only the first rows per thread.
- Workaround: `thread markers --thread t-0` after a `zoom push` onto the window found through a cheaper `--search identity_cb` on one thread.

## Question (review): which `t-N` is a link's `thread=zv`?

- Context: checking a report's links to markers on other threads (`thread=zv`, `A2`, `C1`) in a session loaded from a link on `thread=0`.
- Command: none answers it. `thread list` and `profile info` print `t-N`, never the URL's thread token, and `status` shows only the selected thread's handle.
- Workaround: decode the profiler's base-32 URL encoding by hand (`zv` = 127, `A2` = 130, `C1` = 193), then confirm with `thread markers --thread t-N` and the marker's `markerIndex`.
- What would have answered it: `thread list` printing each thread's URL token, or `--thread` accepting it (`--thread url:zv`).
- Also: without `PROFILER_CLI_SESSION_DIR`, `load` fails in the agent sandbox (EPERM on `~/.profiler-cli`). The error names the fix, but the review brief does not, so every sandboxed reviewer hits it once.
