## Default session directory is not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_bug1725416.html profiler-cli load <taskcluster url> --session test_bug1725416.html-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error does say to set `PROFILER_CLI_SESSION_DIR`, which worked, but every later call then needs it too, and `profile-link.py` as well.
- Workaround: `export PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` in every command.

## Empty call tree on a zoomed range with no samples, without saying so (review)

- Command: `profiler-cli zoom push m-22 --session review-test_bug1725416.html-2` (a 29.5 ms Runnable on the parent main thread), then `profiler-cli thread samples-top-down --session review-test_bug1725416.html-2`, and again with `--include-idle`.
- Expected: the call tree, or a line saying the range holds 0 samples.
- Got: the header `Top-Down Call Tree:` and nothing under it. It could have meant a broken filter or zoom just as well as no samples.
- Workaround: `thread markers` in the same range (`ChromeUtils.importESModule` of `SpecialPowersParent.sys.mjs` plus ~5,900 `Preference Read` markers) showed what the task was doing.
