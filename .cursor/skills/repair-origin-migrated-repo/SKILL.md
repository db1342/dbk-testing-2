---
name: repair-origin-migrated-repo
description: Diagnoses a failed git push or a pull request that cannot be created or updated, and can repair it in certain cases. Invoke manually with /repair-origin-migrated-repo.
disable-model-invocation: true
disabled-environments:
  - cloud
---

# Repair an Origin-migrated repo

Some failures to push to git or manipulate pull requests come from the move from GitHub to Origin, Cursor's source control. Follow this procedure in order.  Not for use in cloud agents, if you are run in a cloud agent you should abort.  If this skill has previously run and been unsuccessful, do not reattempt it within that chat session.

## Check the repo URL

```bash
git remote get-url origin
```

If the hostname is github.com, read references/github.md and follow the instructions it contains.  If the hostname is origin.cursor.com, read references/origin.md and follow those instructions.  If it is something other than these, or you encounter some kind of error, this skill can't help and you should abort.