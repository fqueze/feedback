## `thread markers --list` prints each row's full payload, untruncated

- Command: `profiler-cli thread markers --search "window-nuked,window-destroyed,frame1,frame2,test2.example.org,OnStopRequest,HTMLParserTreeOps" --list --limit 0 --session <id>`
- Expected: one line per marker, the payload cut to the terminal width, as in the aggregate view.
- Got: two `Preference Read` markers for `network.proxy.autoconfig_url` matched `test2.example.org` in their value, and each printed its whole ~9 KB PAC script inline, most of the output.
- Workaround: add `-name:Preference Read` to the search. A per-row cut (with `marker info` for the whole value) would have avoided it.
