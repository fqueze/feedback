## "When did these 6.7 M markers happen?" needs a zoom loop

- Question: in which seconds of the profile the `DocAccessible::ContentRemovedNode` flood (6.7 M markers) happened, i.e. whether it overlapped the failing click.
- Commands: for each 1 s bucket, `profiler-cli zoom push A,B --session S; profiler-cli thread markers --search ContentRemovedNode --session S; profiler-cli zoom pop --session S`, then read "— N markers (filtered". Ten round trips per profile, each several seconds on a 7 M-marker thread.
- Expected: the aggregate `thread markers` view to show, per marker name, a coarse time histogram or first/last timestamps (its "Frequency Analysis" gives only markers/sec and interval stats).
- Also: the earliest time covered by markers (the buffer wrapped at ~7 M markers, dropping the failing subtest) is not stated anywhere. `profile info` reports "Full: 34.243s", while samples and most markers only exist for the last ~4 s. A "markers/samples retained from t=…" line would have saved several checks.
