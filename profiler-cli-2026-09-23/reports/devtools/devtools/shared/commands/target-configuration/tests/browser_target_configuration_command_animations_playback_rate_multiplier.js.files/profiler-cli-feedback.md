## Session name derived from a long report name overflows the Unix socket path

- Command: `PROFILER_CLI_SESSION_OWNER=browser_target_configuration_command_animations_playback_rate_multiplier.js profiler-cli load <url> --session browser_target_configuration_command_animations_playback_rate_multiplier.js-1`
- Expected: session starts (the brief asks for `<report name>-1` session names).
- Got: `The Unix socket path for this session is 110 bytes, over this platform's 107-byte limit`. The error message is clear and suggests PROFILER_CLI_SESSION_DIR.
- Workaround: shorter session id (`tcc-apbrm-1`). Hashing/truncating the socket file name internally would let any session id work.
