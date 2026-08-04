# Reusable Workflows

Because I'm tired of updating every individual repository by hand every time. It's always the same couple of fixes for the same workflows and god forbid anything has a vulnerability referenced.

Now there's just one place to manage my workflows that can be used across orgs/repos, without copy/pasting everywhere.

These explicitly work for my projects, they're pretty flexible, but I'll not be taking any PRs.

Welcome to Insanity.

## Notes

### Auto Scripts

There are two auto scripts.

- `auto_sync.yml` - Will keep all other forked repos in sync with the base repository.
  - Also cleans up any actions performed on the current repo/fork.
  - Runs every 2 hours, 12 minutes off the hour.
- `auto_update_wrangler.yml` - Will query all other repos within the org/user that have the label `wrangler` or `cloudflare` and update them if necessary.
  - **Update Runtime Coverage**:
    - wrangler
    - @cloudflare/workers-types
    - Project types (if project has `types` package script)
    - `.node-version` (if enabled)
      - Can also be disabled per repo with the presence of a `.node-lock` file in the given repo.
    - Package overrides (if specified)
      - See `README.md` in `wrangler-updates` for details.
    - Hono (if enabled)
  - **Notes**:
    - This action will skip any forks, even if your fork is ahead. This is just how Github works. You must leave the fork network otherwise.
    - The action assumes your node runtime is located in the same folder as your `wrangler.toml` file. If it's missing the sub action will fail.
    - This repo is rigged for when dependabot updates a dummy node instance (located in `wrangler-updates`), it will update all other other repos.
      - After the PR has been merged.
    - It's best if you have Cloudflare Workers Pro if you own a lot of repos, as it will trigger all the wrangler builds at once.
      - The max that the free plan can use is 1 build at a time, where as Pro is 6 builds. [See Limits](https://developers.cloudflare.com/workers/ci-cd/builds/limits-and-pricing/).
      - If you don't have Workers Pro, it's advised that you set `AUTO_COMMIT` to `"false"`

### Github Secrets

**the big tl:dr**: If you want to use the secrets without explicitly passing them on an organization level, you _must_ fork this repo.

Otherwise, secrets **will not** transfer automatically **even when** the environment already exists in the repo.

If you do not want to fork, you must manually pass any  secrets. Most actions in this repo (besides `auto_update_wrangler`) support passing secrets.

You must still match the desired environments in the cases of `vars.` usage, regardless.

#### Limitations

Secrets are funny, there are some undocumented quirks about them:

- You can only _manually_ pass secrets stored on the repo/org level.
  - Meaning, you **cannot** load an environment and use secrets defined from there.
    - There are bypasses to this, but they slightly compromise the integrity of the secrets store.
- **However**, secrets stored in environments will _automatically be passed implicitly_ if the action is called with `secrets: inherit`
  - This behavior does chain properly to sub-actions as well.
  - _Only the initial caller_ has to specify the inherit option.
- If you want to use inherit as an organization, you must fork this repo.
- You _do not_ need `secrets: inherit` for the use of `GITHUB_TOKEN`, so long as your action permissions have the same permissions or more as the action itself.

---

### Forks

You must:

- manually enable actions in the fork before any other actions run
  - if they do run, you must manually delete all action runs and refresh the page
- manually enable autosync's workflow

Once done, it may take a little over a day before any scheduled actions run.

---

### On Runtime

| File | Group | Cancelable | Queue Max | Environment Names |
| --- | --- | --- | --- | --- |
| `build_jekyll.yml` | `jekyll_build` | false | single | jekyll |
| `build_openapi.yml` | `openapi` | false | max | openapi |
| `build_sitemap.yml` | `sitemap` | false | max | sitemap |
| `clean_actions.yml` | `clean_actions` | true | N/A | N/A |
| `clean_deploys.yml` | `clean_deploys` | false | max | N/A |
| `clear_cache.yml` | `cloudflare_cache` | false | max | cloudflare |
| `auto_sync.yml` | `autosync` | true | N/A | production |
| `auto_update_wrangler.yml` | `wrangler` | false | single | tangled, wrangler-update |

Max concurrency queueing is 100 jobs.

Environment needs are usually specified in each action file near the top, but you can also just look for the existances of `${{ vars. }}` to figure out the names and what the values should be.
