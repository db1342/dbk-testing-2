You should have established that the repo's remote URL hostname is github.com.  Follow these instructions in order.

## Check Origin auth

```bash
origin auth status
```

There are four possible outcomes. Follow the instructions for the one you see.

### Exit 1 prints that the user is not logged in.

Instruct the user to run `origin auth login`, and tell them they must log into their @x.ai account, not their
@anysphere.co account.  Do not attempt to run this in your agent shell, it is unlikely to succeed there.  When
the user reports having performed this step, repeat the test above, and act in accordance with what you find.

### The user is logged in, and they have an @x.ai email address.

This check has succeeded.  Proceed to the "Determine Mirror Status" step.

### The user is logged in, and they have an @anysphere.co email address.

The user is authenticated, but to the wrong account.  Instruct them to do the following.  Do not attempt these
steps in your agent shell, they are unlikely to succeed.

* In their main browser session, go to xai.okta.com and open the Cursor tile.  Stress that already being signed into
Cursor using their anysphere.co account does not suffice.
* Run `origin auth logout` in their shell
* Run `origin auth login` in their shell

When they report success, run the auth status check above again, and act in accordance with what you find.

### Something Else

Maybe they are signed into some other account, maybe something beyond the scope of this skill is wrong with
their setup.  Explain that this skill doesn't know how to help, and abort.

## Determine Mirror Status

You should now have verified that the user is signed into Origin, using their @x.ai account.

It is possible that the reason this github repo has ceased to be accessible is because it has been mirrored into Origin.
We must determine whether this is the case.

Say to the user:
This command will help me determine whether the repo has been transitioned to Origin.

```bash
origin repo from-github
```

The output should tell you whether the repo has been mirrored, and if so what the corresponding origin URL is,
and the mirror status (outbound vs inbound).  If something else happens, this skill cannot help, and you should abort.

If the repo is mirrored, and if the mirror status is outbound, then mirroring is the reason the user cannot push
or make PRs.  In that case you should read `./github_mirror.md` and follow the instructions it contains.

If the repo is not mirrored, or if the mirroring is "inbound", this is not the reason the user is struggling to
push and make PRs.  This skill cannot help, and you should abort.