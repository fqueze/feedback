## Question: how long from marker A to the next marker B, for each occurrence?

- Command: `profiler-cli thread markers --search 'NotifySaveComplete,DownloadDoneResolve' --list --limit 0`.
- Expected: a way to pair each `BackgroundFileSaver::NotifySaveComplete` with the next `DownloadPlatform::DownloadDoneResolve` and print the gap. That gap is how long the unprofiled background task took for each download.
- Got: the flat list. Pairing them by eye over 20+ downloads is slow and error-prone.
- Workaround: `--json` and a 10-line Python script pairing each B with the latest A before it.
