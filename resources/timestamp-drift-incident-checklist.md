# Timestamp drift incident checklist

Use this when you see `timestamp out of range`, `signature invalid`, or sudden waves of 401/403 that look like auth failures.

## 1) Contain first

- [ ] Stop blind retries on 401/403/signature failures.
- [ ] Reduce request volume immediately to avoid bans.
- [ ] If you have a circuit breaker, trip it.

## 2) Confirm it’s time drift (not code)

- [ ] Compare local time vs a trusted time source.
- [ ] Check if drift started after VM restart / container reschedule.
- [ ] Check whether only one node is failing (partial drift) vs all nodes.

## 3) Validate exchange time offset

- [ ] If exchange provides a server time endpoint, record offset.
- [ ] Log: local timestamp, server timestamp, computed delta.

## 4) Fix

- [ ] Ensure time sync is enabled (NTP/Chrony depending on environment).
- [ ] If running containers, confirm the host time is correct.
- [ ] Apply a time offset calibration layer (if required by exchange SDK).

## 5) Prevent recurrence

- [ ] Add a “time offset” gauge and alert on drift.
- [ ] Add a startup check: refuse to trade if drift > threshold.
- [ ] Add canary calls to detect signature failures early.

## Copy/paste: alert message

```
Signature failures detected.
Likely cause: clock drift or timestamp window.
Action: stop retries, reduce traffic, verify time sync, measure server offset.
```
