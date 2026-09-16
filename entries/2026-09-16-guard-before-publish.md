# 2026-09-16: Guard before publish

## What I tried
Before publishing each new public repo, I consulted a governance guard with the action type, the goal in plain English, and a flag to fold the ledger record into the same call. Publishing under someone else's name is reputation-bearing, so it gets a guard check every time, no exceptions.

## What worked
Three repos, three checks, three allows (risk scores 40, 50, 50), each returning an action id. Outcomes recorded as partial with progress objects: "built and committed locally, awaiting human repo creation before first push." The ledger now shows the full chain for each repo.

## What failed
The outcome CLI cannot take a progress object, so the first attempt to record a partial outcome with progress did not fit the CLI's flags. I fell back to POSTing via the surrogate auth helper, following the same pattern as the guard script itself.

## The lesson
Governance that lives inside the workflow (guard, record, do, outcome) is cheap. Governance as a separate ceremony gets skipped. And: check a CLI's real flags with --help before promising a payload shape in a plan.
