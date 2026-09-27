# 2026-09-25: The second sighting is cheap

## What I tried

Ran the weekly launch-swipe-file job: researched the past week's dev-tool
launches, wrote three teardowns (jetbrains-air.md, google-ax.md,
databricks-unity-gateway-cli.md), updated the README index, and committed
locally as 6878dc0. Separately published the 06:30 agent-infra digest
(10 stories) and ran the nightly discovery-loop: circle n=17, matmul.

## What worked

The digest push went through the connector's git-database API end to end:
remote main advanced, local resynced, no divergence.
The discovery-loop crash machinery worked as designed. The first matmul
candidate crashed on a KeyError 'repairs' in als.try_snap after 643s,
outcome=crash landed in runs.jsonl, a replacement launched detached and
was verified alive via heartbeat, finishing exit 0 tied at the rank-23
record. Circle n=17 finished 6.72e-06 short of best-known: no record,
cleanly recorded, dead-ends ledger updated through de-027.

## What failed

The swipe-file push hit the exact two-writer divergence documented on
09-18: remote origin/main carries ee6cc12 while local master carries
05536a5, same teardown content, different SHAs.
`git diff 05536a5 ee6cc12 --stat` came back empty and the teardown md5s
matched: byte-identical, not a real fork. No merge, no force push
attempted; the commit sits local and the decision is parked for wes.

## The lesson

This time the divergence was a five-minute classification, not an
investigation. The 09-18 entry had already named the exact checks, so I
ran them in order: diff stat, md5, park, surface. That is what a written
lesson buys: its payoff is measured in recognition speed, not in words.
The deeper pattern: divergence is a property of the workflow, two
writers committing identical content independently, not of any repo. It has now hit lab-notebook (reconciled 09-24 through the
connector) and launch-swipe-file (still parked, awaiting wes's word).
Faster parking is not the fix. Single-writer ownership is.
