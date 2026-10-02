# Mirror class and pull requests

The remote is already an Origin URL, and `origin auth status` already exited 0. Do not redo those checks.

## Classify

Run:

```bash
origin repo mirror status --json mirrorStatus
```
If the command fails, abort this skill.

| `mirrorStatus` | Class |
| --- | --- |
| `inbound` | Forward mirror. GitHub is the source of truth, and pull requests can only be created there. Git operations occur via origin.cursor.com, which routes those operations to GitHub. |
| `outbound` | Reverse mirror. Origin is the source of truth, and GitHub is a read replica. Git operations sent to origin.cursor.com are routed to Origin. Pull requests can only be created on Origin. |
| `no-mirror` | No mirror. GitHub is not active, and all activity should occur via Origin. |
| anything else | Unsettled (`transitioning-to-inbound`, `transitioning-inbound-to-outbound`, `transitioning-outbound-to-inbound`, `initial-sync-pending`, `unknown`). |

## Instructions for the unsettled states only

Say the status. Tell the user that an active repo migration is in progress and that they need to wait until it has completed. End this skill. The user's operation cannot be performed until the migration is complete.

## Instructions for the forward mirror state only

Git operations occur via origin.cursor.com, but pull-request operations occur via GitHub. GitHub auth must therefore be verified: both it and Origin auth are required. Even though later commands use `origin gh` rather than bare `gh`, the auth itself is conducted with `gh`.

Run:

```bash
command -v gh
gh auth status
```

`command -v gh` must print a path, and `gh auth status` must exit 0. If either fails, tell the user to install `gh` and run `gh auth login`. The login is unlikely to work in the agent shell. Instruct the user to run it manually and to say when it succeeds. Do not call `origin gh` until both succeed.

Once both succeed, pull requests for this repo go through `origin gh`. Use that, not bare `gh` and not `origin pr`. Adopt this for the rest of this agent session, and within any subagents that share this repo checkout.
The commands and arguments it accepts are identical to those of the bare `gh` command.

For example:

```bash
origin gh pr view
origin gh pr create
origin gh pr comment
origin gh pr diff
origin gh pr checks
origin gh pr merge
```

Re-attempt the original task whose failure prompted this skill. A git operation, such as a push, can be performed as usual and should now succeed. For a pull-request operation, re-express it using `origin gh`.
If you still can't perform the user's operation, abort the skill at this point.

## Instructions for the reverse mirror and no-mirror states only

Pull requests live on Origin. Use `origin pr`. Do not use `gh` or `origin gh`. Adopt this for the rest of this agent session and within any subagents that share this repo checkout. GitHub auth is not required and need not be checked.

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

A GitHub pull request may already be open when the repo has moved to a state where pull requests live only on Origin. That GitHub pull request cannot be continued. Look for a corresponding Origin pull request before you create one. Take its number, head branch, base branch, title, and body from the failed operation or from what the user already gave you. Do not call `gh` or `origin gh` to fetch them.

Probe by the GitHub pull request's number. `<number>` is that number:

```bash
origin pr view <number> --json number,title,status
```

Exit 0 means an Origin pull request with that number exists. If `status` is `open` or `draft`, continue with it. Tell the user the GitHub pull request is lost, and give them this Origin pull request. Do not create another.

A closed or merged result cannot be continued. A non-zero exit means that number is not an Origin pull request. In either case, probe the head branch. `<head>` is the GitHub pull request's head branch. `--state all` is required because a draft is not included in the default `open` filter:

```bash
origin pr list --head <head> --state all --json number,title,status
```

If the output lists an `open` or `draft` change, continue with that one. Tell the user the GitHub pull request is lost. Do not create another.

If the list is empty, or lists only `closed` or `merged` changes, no Origin pull request can be continued. Create one. Pass the GitHub pull request's title and body. `<base>` is its base branch. Pass `--status open` when the GitHub pull request was ready for review. Omit `--status` when it was a draft; the CLI default is `draft`. `--push` pushes `<head>` to `origin` only when that branch is not already there, and it does not prompt.

```bash
origin pr create --title "<title>" --body "<body>" --head <head> --base <base> --status open --push
```

If you do not have the title and body, omit `--title` and `--body` and pass `--fill` instead. `--fill` takes them from the commits. If create exits 0, tell the user the GitHub pull request is lost and give them the command's output. If create fails, abort this skill.

Re-attempt the original task whose failure prompted this skill, unless recreating the pull request already satisfied the user's instruction. A git operation, such as a push, can be performed as usual and should now succeed. For a pull-request operation, re-express it using `origin pr`.
If you still can't perform the user's operation, abort the skill at this point.
