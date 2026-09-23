## Session names near the socket-path limit

- Command: `profiler-cli load <url> --session test_ext_contentscript_triggeringPrincipal.js.shutdownthreadpool-crash-X3j8`
- Expected: a session; the brief asks for `<report name>-N` names, and report names like this one are 70 characters.
- Got: `Error: The Unix socket path for this session is 108 bytes, over this platform's 107-byte limit`. In a script the next `thread markers --json` call then printed nothing to stdout, so my parser failed on empty JSON before I saw why.
- Workaround: short suffixes (`-4`). The limit could be avoided by hashing long session names into the socket path.
