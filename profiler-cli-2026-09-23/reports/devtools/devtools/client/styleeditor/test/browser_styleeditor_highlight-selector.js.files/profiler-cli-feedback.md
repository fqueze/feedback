## Question: "did this child process (pid 6680) ever talk to the parent?" (2026-09-22)

- `profiler-cli thread markers --session <s> --search "otherPid:6680" --list --limit 0 --json | python3 ...` (split by `data.direction`), then the same for `ACCEPT_INVITE_MESSAGE` to list which pids ever connected.
- Expected: a way to see, per child pid, messages sent / received and the first received time, from the default output.
- Got: the text list shows 63 rows of `IPCOut ... 0s` with no direction or message type in the row, so "0 received" and "first message at t" needed a script. The dead process has no track in `profile info`, so nothing else says a launched process never connected.
- Could have shown: an IPC summary per other pid (sent/received counts, first/last time), or `profile info` listing launched child pids that have no track / never sent ACCEPT_INVITE.
