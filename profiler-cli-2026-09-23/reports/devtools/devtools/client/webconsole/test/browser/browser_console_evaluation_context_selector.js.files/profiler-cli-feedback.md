## `zoom push` with minute-formatted times silently zooms to a wrong 5 s range

- Command: `profiler-cli zoom push 19m,24m55s --session <s>` (times copied from `thread markers` output, which prints `t=19m58s`)
- Expected: a zoom to 19 min – 24 min 55 s, or an error saying `m` is not a supported unit.
- Got: `Zoom depth: 1` with no warning; `status` then showed `View range: ts-1 to ts-2` and the marker list header `(5s)`, so the following search returned 0 markers, which looked like a real absence.
- Workaround: converted to seconds (`zoom push 1140,1495`). Either accept the `XmYs` format that the marker list prints, or reject it.
