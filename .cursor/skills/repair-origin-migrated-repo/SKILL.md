---
name: repair-origin-migrated-repo
description: Diagnoses a failed git push or a pull request that cannot be created or updated, and can repair it when the failure comes from a GitHub-to-Origin migration. Use when git push fails, or a pull request cannot be created or updated, on a github.com or origin.cursor.com remote.
disabled-environments:
  - cloud
---

# Repair an Origin-migrated repo

Some failures to push to git or manipulate pull requests come from the move from GitHub to Origin, Cursor's source control. Follow this procedure in order.  Not for use in cloud agents, if you are run in a cloud agent you should abort.  If this skill has previously run and been unsuccessful, do not reattempt it within that chat session.

## Announce your intention
Say the following to the user, but proceed directly to the shell command which follows it, in the same turn.

A possible reason why pushing to this repo or making PRs is not working is that this repo has been migrated to Origin,
Cursor's native git forge.  I'll now check to see if that is the case, and if so help you adapt to this transition.

## Check the repo URL

```bash
git remote get-url origin
```

If the hostname is github.com, read references/github.md and follow the instructions it contains.  If the hostname is origin.cursor.com, read references/origin.md and follow those instructions.  Read only that one file.  Leave the other references unread until the file you are following tells you to open one.  If the hostname is something other than these, or you encounter some kind of error, this skill can't help and you should abort.