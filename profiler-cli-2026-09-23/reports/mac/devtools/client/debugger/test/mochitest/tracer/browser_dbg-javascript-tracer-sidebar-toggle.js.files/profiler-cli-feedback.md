## Question: which markers in a range have function X in their stack?

- Command: `profiler-cli thread markers --has-stack --list --limit 0 --json` after `zoom push 13.70,14.279`, then one `profiler-cli marker stack <handle>` per marker (1,031, 1,553 and 1,127 markers; ~0.18 s each, 3-5 min per profile), then grep for `setCanRender`.
- Expected: a way to filter markers by a function in their stack (e.g. `thread markers --stack-search setCanRender`), or `marker stack` taking a range of handles like `marker info m-a..m-b`.
- Got: `--search` matches name/category/payload only; `marker stack` takes one handle.
- Could have shown: "no marker from the registration to the end of the profile has `setCanRender` in its stack", which is the core negative observation here, in one call.

## `thread markers --search` on a runnable name also matches IPC markers

- Command: `profiler-cli thread markers --search "PBackgroundIDBTransaction::Msg_Complete,TimeoutExecutor" --list`
- Expected: the Runnable markers with those names.
- Got: also dozens of `IPCOut`/`IPCIn` markers (their payload holds the message type), 35 KB of output.
- Workaround: grep the rows for `Runnable`.
