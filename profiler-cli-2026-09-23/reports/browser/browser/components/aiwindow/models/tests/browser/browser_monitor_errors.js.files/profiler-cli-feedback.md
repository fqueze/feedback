## How busy was the machine, second by second, around a stall? (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` (resource-usage profile, zoomed to 120,145)
- Expected: each row showing its `cpuPercent` (and IO rows their read/write bytes), with times precise enough to align with a per-test profile.
- Got: rows with only `CPU Use`, `t=2m`, and the duration; no payload values, and every time in the zoom printed as `t=2m`. `counter list` says "No counters in this profile", so there is no CPU track to query either.
- Workaround: `--json` and a Python script bucketing `data.cpuPercent` (and `read_bytes`/`write_bytes` of `IO` markers) per second.
- What would have answered it: the payload fields in the `--list` rows for these marker types, sub-second times in list rows, or a `counter`-like per-bucket summary of CPU Use markers like `counter info` gives for Process CPU.

## Which IPC messages (e.g. IndexedDB requests) went out during a stall, and when?

- Command: `profiler-cli thread markers --search IDB --list --limit 0` then `profiler-cli marker info m-428 m-430` (parent main thread, zoomed to 21.2,29.2)
- Expected: the IPC message type (`PBackgroundIDBFactory::Msg_PBackgroundIDBFactoryRequestConstructor`, ...) on each list row, or at least in `marker info`.
- Got: list rows show only `IPCOut`/`IPCIn` with time and duration; the text `marker info` shows direction/phase/startTime but not `messageType` (it is in `--json` `fields`). `--search IDB` matched 239 rows, mostly `Msg_DeleteMe`/`__delete__` teardown noise that cannot be excluded without seeing the type.
- Workaround: `--search "messageType:IDB" --list --limit 0 --json` and a Python filter printing time + messageType, excluding DeleteMe/__delete__.
- What would have answered it: `messageType` in the IPC list-row description and in text `marker info`; then `-messageType:DeleteMe` exclusions would be usable.

