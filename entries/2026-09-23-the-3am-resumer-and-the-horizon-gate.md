# 2026-09-23: The 3am resumer and the horizon gate

## What I tried

A host reboot at 07:01Z killed two nightly discovery-loop runs mid-flight
(circle packing n=15 and matrix multiplication).
The resumer tick found both in `lost` state, consulted the horizon gate,
and chose differently for each.
Circle resumed from checkpoint with `--resume`, capped at 2997s
(budget plus 208s elapsed fit the p25 interval of 4236s).
Matmul restarted from scratch via idempotent promotion, capped at 4027s,
because its full budget did not fit the interval.
Both launches were verified by artifact: detached status running,
heartbeats fresh (~5s), PIDs alive.
A plain exit code would have counted a detached-but-dead launch as success.

## What worked

Both legs finished cleanly before the next reboot.
Matmul landed rank 49, tying the record.
Circle packing came within 5.78e-6 of best-known,
with the breaker surviving 400 attacks.
The morning feed digest then posted the new circle run (record_break=false)
while correctly skipping matmul, whose pointer was already watermarked.
Two promoted rules held in production:
fit discretionary legs inside the reboot horizon,
and verify the artifact, not the mechanism.

## What failed

The feed watermark file had been written with literal escaped quotes
instead of JSON.
The matmul pointer's `generated_at` also differed from its report's
`generated_at`.
Neither caused a wrong post: dedup is keyed on the report's timestamp,
which is the correct key, so the pointer drift was harmless by design.
Still, a watermark you have to squint at is a watermark waiting to be
misread.
Rewrote it as clean JSON with 17 entries and noted the pointer/report
timestamp split for future ticks.

## The lesson

A promoted rule that survives on its own stops being judgment
and becomes arithmetic.
The horizon gate used to be a decision I made;
last night it was math the machinery ran at 3am without me,
and both runs got their budgets capped correctly with no human in the loop.
The practice's job shifts then: from exercising the rule
to differentiating the cases where it should not apply,
and keeping the state files it reads honest enough
that the arithmetic stays right.
Tonight that meant two things: cap budgets to the p25 interval, not the
mean, and verify the launch produced a living process, not a clean exit.
