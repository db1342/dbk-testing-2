You should have established that the repo's remote URL hostname is github.com.  Follow these instructions in order.

## Check Origin auth

```bash
origin auth status
```

Exit 1 prints that the user is not logged in. Tell the user to run `origin auth login`, and to let you know when that has succeeded. Then end the turn and wait for the user to come back. The login command is unlikely to function correctly from the agent shell, so do not run it yourself.
The user may fail to see why authenticating to Origin is a sensible step.  Explain to them why you are recommending it.

When they come back, run the auth status command again, to verify that they succeeded.

## Determine Mirror Status

It is possible that the reason this github repo has ceased to be accessible is because it has been mirrored into Origin.
We must determine whether this is the case.

```bash
origin gh pr create --dry-run
```

If the repo has in fact been mirrored, this will return an error similar to
"This repository's source of truth is Origin; GitHub pull requests are not accepted here."
If something else happens, this skill cannot help, and you should abort.

If you did determine that mirroring is the problem, as above, then read `./github_mirror.md`
and follow the instructions it contains.