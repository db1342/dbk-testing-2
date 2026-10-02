You should already have established that this repo has an origin.cursor.com remote url.  Follow these instructions in order:

## Check Origin auth

```bash
origin auth status
```

Exit 1 prints that the user is not logged in. Tell the user to run `origin auth login`, and to let you know when that has succeeded. Then end the turn and wait for the user to come back. The login command is unlikely to function correctly from the agent shell, so do not run it yourself.

When they come back, run the auth status command again, to verify that they succeeded.  If they refuse, do not act on the request, or if the second
auth check fails, abort the skill.

## Once the user is authed

Read `./origin_mirror.md` and follow it. That file classifies the mirror and names the pull-request commands. Do not load it unless the remote's hostname is `origin.cursor.com` and `origin auth status` exits 0.