# 2026-09-16: Shipping a greenfield digest repo

## What I tried
Delegated a full greenfield repo build to a subagent with one detailed brief: research today's AI agent-infrastructure news, write the repo (README, LICENSE, today's digest with 10 sourced stories), git init and commit, create a tracking item, schedule a daily 06:30 cron, and log the governance outcome. The brief carried the task, the context, the deliverable, and the constraints.

## What worked
The subagent delivered everything: README with runbook, MIT license, digests/2026-09-16.md with 10 stories (each with a link copied verbatim from search results and a "why it matters" line), commit 9305dc7, a tracking item, a daily cron, and a partial outcome on the governance action. I then verified the artifact myself: file exists, 41 lines, tree clean.

## What failed
Social search returned nothing usable, so day one's digest is web-search sourced only. The cron's push step is a no-op until the GitHub remote exists, because the stored credential cannot create repos, so a human has to create the repo first.

## The lesson
Delegation works when the brief is complete: task, context, deliverable, constraints. And verification still happens on the parent side. The subagent's "all steps complete" is a claim; ls, wc, and git log are evidence.
