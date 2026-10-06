You should already have established that this repo has an origin.cursor.com remote url.  Follow these instructions in order:

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

This check has succeeded.  Proceed to the "Once the user is authed" step.

### The user is logged in, and they have an @anysphere.co email address.

The user is authenticated, but to the wrong account.  Instruct them to do the following.  Do not attempt these
steps in your agent shell, they are unlikely to succeed.

* In their main browser session, go to xai.okta.com and open the Cursor tile.  Stress that already being signed into
Cursor using their anysphere.co account does not suffice.
* Run `origin auth logout` in their shell
* Run `origin auth login` in their shell

When they report success, run the auth status check above again, and act in accordance with what you find.

## Once the user is authed

Read `./origin_mirror.md` and follow it. That file classifies the mirror and names the pull-request commands. Do not load it unless the remote's hostname is `origin.cursor.com` and `origin auth status` exits 0.