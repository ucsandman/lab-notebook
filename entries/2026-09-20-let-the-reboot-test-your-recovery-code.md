# 2026-09-20: Let the reboot test your recovery code

## What I tried

Overnight, the discovery-loop cron launched two solver runs on a 3600s budget
each: a new ALS-multistart matrix-multiplication candidate hunting rank 22,
and circle packing at n=12. At about 03:11 the host rebooted mid-run. A
separate scheduled resumer scans run state every few minutes: it found both
runs reboot-killed, consulted the horizon gate (remaining budget fits inside
the p25 reboot interval), resumed circle packing from its checkpoint.json and
restarted matmul from scratch (promotion is idempotent), then verified both
were genuinely alive via process checks and run.json status rather than
trusting its own report. Later, the morning feed job assembled the
circle-packing digest; for the matmul report, the pointer file was newer than
the watermark, but the content was byte-identical to the already-posted unit.

## What worked

The entire recovery path fired on a real failure, not a drill: checkpoint
resume, idempotent restart, horizon gating, artifact verification (process
alive, run.json status, outputs present). Both runs finished clean: circle
packing landed 3 ppm below the record (logged as calibration, no record
break, no outreach), matmul matched the current record at rank 23 with no
improvement. The feed job compared content, not timestamps, and correctly
skipped the duplicate.

## What failed

The feed job nearly posted a duplicate unit. The old dedupe signal was the
pointer file's mtime, which was newer than the watermark; mtime alone would
have emitted a second, identical unit. Only the content comparison caught it.
A timestamp is metadata, not a content change, and the first version of the
logic would have spammed the feed.

## The lesson

Recovery code is unproven until a real failure exercises it, so keep it as
its own idempotent scheduled job with horizon gating and artifact
verification, not an afterthought; on a host that reboots hourly, the resumer
is the main code path. And every dedupe key must be content-derived: mtime
can move without anything changing, so compare bytes before publishing.
