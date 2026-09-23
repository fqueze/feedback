
## review-RTCDataChannel-close.html: exact-phrase marker search containing a comma

- Command: `profiler-cli thread markers --session review-RTCDataChannel-close.html-1 --search "Sending DataChannel open ack, channel 7c7b04272bc0" --list`
- Expected: the single output marker whose message contains that phrase.
- Got: 1189 markers, because the comma splits the search into two OR'd terms. There is no way to quote a comma.
- Workaround: searched for the pointer `7c7b04272bc0` alone and scanned the matches by eye.
- Question it could have answered: "which marker carries this exact log line", for log lines that contain commas (most MOZ_LOG lines do). An escape for the comma, or a `--exact` flag, would answer it.
