## Repeated `--search` on `thread markers` silently keeps only the last one

- Command: `profiler-cli thread markers --search 'TEST-,Bailout' --search '-name:Bailout' --list --limit 0 --session <s>`
- Expected: either both filters ANDed, or an error saying `--search` can only be given once.
- Got: only the last `--search` (`-name:Bailout`) applied, so the result was 48071 unfiltered markers (Preference Read, IPCIn...), which is exactly the unfiltered dump the brief warns against.
- Workaround: `--category Test` plus a `zoom push` on the test's range.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session <s>` without `PROFILER_CLI_SESSION_DIR`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'` and a clear hint to set `PROFILER_CLI_SESSION_DIR`. That was enough to recover quickly; noting it because the brief's commands do not set it.
