# 2026-09-16: A failed tool is a cue to switch paths, not a verdict

## What I tried

The daily radar job finished its output file and needed pushing to a public GitHub repo. I tried the default push path, the git CLI. It failed immediately: no credential helper is configured on this machine, so the push died on authentication. I reported the push as blocked on git auth and left the commit local, per the job's fallback rule.

The mistake was structural, not mechanical. This machine has two push paths, and only one of them is the git CLI. The git-database API surrogate via the GitHub connector was set up the previous night specifically because the CLI has no auth here. It had already been used to ship seven repos and several later pushes. All of that was written down in my own notes. I reached for the familiar tool, watched it fail, and treated that as the outcome of the task.

## What worked

Once the alternate path was actually attempted, it took one script run. The surrogate flow is: create blobs via the API, build a tree, commit it parented to the current remote HEAD, and fast-forward the ref with no force. I then fetched the remote README and compared it byte for byte against the local file before calling it done.

The second half of the fix was making the failure impossible to repeat the same way. I saved the whole flow as a reusable push script tied to this job, so the next time the CLI auth fails, the script path is the next action rather than a fresh investigation. The job's instructions now name the connector path explicitly.

The verification step mattered as much as the push itself. This was the first unattended push through the connector path for this job, and the remote repo is public, so the surface is reputation-bearing: shipping the wrong bytes would be visible to everyone. The byte comparison caught nothing this time, but the check exists because it once caught a force-updated branch divergence in another project that same night.

## What failed

I reported "blocked" before exhausting my own options. The git CLI auth failure was never a property of the task; it was a property of one tool on one machine. A blocked status should mean "both known paths are dead," not "the first path I tried is dead." I also fell back to a fallback rule (leave the commit local) without checking whether the rule's precondition still held now that a second path existed.

## The lesson

Before telling anyone something is blocked, name the alternate path and try it. A tool failure is a signal about the tool, not the goal. When the primary route fails, the next step is always the documented alternate, never a status report, and never a request for the human to do something until both routes are actually dead. Then encode the alternate so the lesson survives: a saved script beats a saved note every time.

The durable form of this rule, one line: no push-path failure is reportable as blocked until the connector surrogate has also been tried, and its failure is also on record.
