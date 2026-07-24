# Reusable Workflows

Because I'm tired of updating every individual repository with new action versions.

It's always the same couple of fixes for the same workflows and god forbid any of them have a vulnerability referenced.

So now it's one place to manage my workflows that can be used across orgs/repos.

## Notes

### On Secrets

**tl:dr**: If you want to use the secrets without passing them explicitly in an org, you _must_ fork the repo.

Otherwise, secrets will not transfer automatically even when the environment exists already in the repo.

If you do not want to fork, you must manually pass the secrets.

#### Limitations

Secrets are funny, there are some undocumented quirks about them:

- You can only manually pass secrets that are stored on the repo/org level
- You **cannot** load an environment manually and pass secrets defined from there
  - There are bypasses to this, but they slightly compromise the integrity of the secrets store
- **However**, secrets stored in environments will _automatically be passed_ if the action is called with `secrets: inherit`
  - This behavior does chain properly to chained invocations
  - _Only the initial caller_ has to specify the inherit option
- If you want to use inherit on an org level, you must fork this repo
  - This _may_ only be a restriction for orgs, had mixed success while testing user references
- You _do not_ need `secrets: inherit` for the use of `GITHUB_TOKEN`, so long as your calling actions contain the same permissions

---

### On Forking

You must:

- manually enable actions in the fork before any other actions run
- manually enable autosync's workflow

Once done, it may take a day before scheduled actions run.

---

### On Concurrency

| File | Group | Cancelable | Queue Max |
| --- | --- | --- | --- |
| `build_jekyll.yml` | `jekyll_build` | false | single |
| `build_openapi.yml` | `openapi` | false | max |
| `build_sitemap.yml` | `sitemap` | false | max |
| `clean_actions.yml` | `clean_actions` | true | N/A |
| `clean_deploys.yml` | `clean_deploys` | false | max |
| `clear_cache.yml` | `cloudflare_cache` | true | N/A |
| `autosync.yml` | `autosync` | true | N/A |

Max Queueing is 100 jobs.
