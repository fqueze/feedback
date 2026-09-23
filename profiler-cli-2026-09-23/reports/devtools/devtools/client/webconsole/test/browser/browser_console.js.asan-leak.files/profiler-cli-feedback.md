## `zoom push` with the tool's own time format silently zooms to a zero-length range

- Command: `profiler-cli zoom push 23m4s,23m23s --session browser_console.js.asan-leak-1`
- Expected: a zoom to 23m4s..23m23s (the format `thread markers --list` prints, `t=23m4s`), or an error.
- Got: `Pushed view range: ts-1 (23s) to ts-1 (23s) (duration: 0s)`, and every later query returned nothing.
- Workaround: convert to seconds by hand (`zoom push 1384,1403`).

## Question: "what did LSan suppress in this process, and how does that compare with a clean run?"

- Command: `thread markers --search "LSan" --list` gives `LSan Leak` / `LSan Summary` markers, but the `Suppressions used:` table (count, bytes, rule) is only in plain `output` markers, one per line.
- Why it mattered: in the leaking job the parent's table had `XPCWrappedNativeJSOps` 121 allocations, `mozJSModuleLoader` 6, `xpc::CIGSHelper` 5, `nsComponentManagerImpl` 135. In the clean jobs it had none of the XPConnect rules and 7 for `nsComponentManagerImpl`. So the leak was about 10 times bigger than the 36 reported allocations. Finding that took dumping every log marker of the manifest from two profiles and diffing them.
- What would have shown it: carry the suppression table on the `LSan Summary` marker, e.g. as fields.

## Question: "which frames do these 28 LSan leak stacks have in common, and which ones stand out?"

- Command: a shell loop over `marker stack m-3` ... `m-35`, then `sort | uniq -c` on the frames.
- What would have shown it: a way to aggregate stacks across a marker search, like a call tree built from marker stacks instead of samples (for example `thread markers --search "LSan Leak" --stack-tree`).
