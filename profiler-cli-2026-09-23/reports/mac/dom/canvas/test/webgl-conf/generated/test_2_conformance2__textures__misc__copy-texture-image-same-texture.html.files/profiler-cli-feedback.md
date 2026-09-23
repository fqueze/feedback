
## `--session` before the subcommand is rejected with a misleading hint (review-test_2_conformance2__textures__misc__copy-texture-image-same-texture.html)

- Command: `profiler-cli --session review-copytex-1 profile info`
- Expected: the session option accepted in any position, like a global option, or an error that says it goes after the subcommand.
- Got: `error: unknown option '--session' (Did you mean --version?)`
- Workaround: put `--session <id>` after the subcommand (`profiler-cli profile info --session review-copytex-1`).
