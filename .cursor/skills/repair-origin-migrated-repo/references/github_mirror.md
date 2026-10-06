You should have determined that this is a repo with a github.com remote URL, which is not usable because
It has been migrated to Origin.  The origin command line tool should have been authenticated.

## Change the repo's remote URL

Do not ask for approval in the chat. A sentence such as "approve this and I will run it" does not show a dialog and is not approval. Invoke the shell tool on this command in the same turn:

```bash
origin repo set-origin-remote --local
```

That invocation is what raises the shell command approval dialog. Wait for the user to accept or reject it there.

If Auto-review blocks the command, immediately retry that exact command with the shell tool's approval-card retry set, and pass the block reason through unchanged. That retry is the native approval card. Do not replace it with a chat question.

If the user rejects the dialog, or the command fails, do not try to problem solve and diagnose. Say you don't know how to help, and abort the skill.

Once you've done this, you can reattempt the user's original operation.  If it's a git operation, for example
a push, you can do it the usual way and it should now succeed.  PR operations however are now done using the
`origin pr` command line tool, and not `gh`.

For example:

```bash
origin pr create
origin pr list
origin pr view
origin pr diff
origin pr checks
origin pr edit
origin pr comment
origin pr merge
```

A GitHub pull request may already be open when the repo has moved to a state where pull requests live only on Origin.  That GitHub pull request cannot be continued.  Look for a corresponding Origin pull request before you create one.  Take its number, head branch, base branch, title, and body from the failed operation or from what the user already gave you.  Do not call `gh` or `origin gh` to fetch them.

Probe by the GitHub pull request's number.  `<number>` is that number:

```bash
origin pr view <number> --json number,title,status
```

Exit 0 means an Origin pull request with that number exists.  If `status` is `open` or `draft`, continue with it.  Tell the user the GitHub pull request is lost, and give them this Origin pull request.  Do not create another.

A closed or merged result cannot be continued.  A non-zero exit means that number is not an Origin pull request.  In either case, probe the head branch.  `<head>` is the GitHub pull request's head branch.  `--state all` is required because a draft is not included in the default `open` filter:

```bash
origin pr list --head <head> --state all --json number,title,status
```

If the output lists an `open` or `draft` change, continue with that one.  Tell the user the GitHub pull request is lost.  Do not create another.

If the list is empty, or lists only `closed` or `merged` changes, no Origin pull request can be continued.  Create one.  Pass the GitHub pull request's title and body.  `<base>` is its base branch.  Pass `--status open` when the GitHub pull request was ready for review.  Omit `--status` when it was a draft; the CLI default is `draft`.  `--push` pushes `<head>` to `origin` only when that branch is not already there, and it does not prompt.

```bash
origin pr create --title "<title>" --body "<body>" --head <head> --base <base> --status open --push
```

If you do not have the title and body, omit `--title` and `--body` and pass `--fill` instead.  `--fill` takes them from the commits.  If create exits 0, tell the user the GitHub pull request is lost and give them the command's output.  If create fails, say you don't know how to help, and abort the skill.

Re-attempt the user's original operation, unless recreating the pull request already satisfied their instruction.  A git operation, such as a push, can be performed as usual and should now succeed.  If it was a pull-request operation, re-express it using `origin pr`.  Use `origin pr` for all pull-request operations in this agent session going forward.