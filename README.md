# Reusable Workflows

Because I'm tired of updating every individual repository with new action versions.

It's always the same couple of fixes for the same workflows.

And god forbid any of them have a vulnerability in them.

So now it's one place to manage my workflows that can be used across orgs/repos.

---
## On Organization Secrets

**NOTE**: If you want to use the secrets without passing them explicitly in an org, you _must_ fork the repo.

Otherwise, secrets will not transfer automatically even when the enviroment exists already in the repo.
If you do not want to fork, you must manually pass the secrets.

Be warned, you can only manually pass repository secrets. You cannot manually pass a secret stored in an environment.
(but you can secrets stored in environments if you call an action with `secrets: inherit`)

---
### On Forking

You must manually enable actions in the fork before any other actions run.

You must also manually enable autosync's workflow. 

Once done, it may take a day before scheduled actions run.
