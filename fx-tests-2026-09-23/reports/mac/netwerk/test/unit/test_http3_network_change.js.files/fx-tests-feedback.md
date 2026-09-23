## Question: "can I load this profile?"

- Command: `fx-tests task A5PNp98bT2m_eFo7DQeSzw --profiles`
- It lists the per-test profile URL but not its size. This test's per-test profiles are 48–54 MB gzipped and about 870 MB decompressed, too large for `profiler-cli load` (which died without an error). Finding that out cost two load attempts and a 120 s hang.
- What the output could show: the artifact's size (Taskcluster's `x-goog-stored-content-length`, or the `profiler Symbolicating ... (771490530 bytes)` line some logs carry), with a warning above a few hundred MB.
