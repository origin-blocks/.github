# Origin shared workflows

## Deploy

`.github/workflows/deploy.yml` is the one deploy for Origin sites. It logs in to the server as `deploy`, checks the server's key against a pinned copy, and runs one script from `/home/deploy/bin` with the pushed ref and commit. Those scripts, and the one sudo rule each needs, are in the private server repo.

Each site repo has one `.github/workflows/deploy.yml` like this:

```yaml
name: Deploy

on:
  push:
    tags: ['[0-9]*.[0-9]*.[0-9]*']

jobs:
  deploy:
    uses: origin-blocks/.github/.github/workflows/deploy.yml@v1
    with:
      live: deploy-canvas-demo
      live-tags: true
      live-branches: ''
    secrets: inherit
```

A release tag (`1.2.3`) deploys the live site when `live-tags` is true. `master` or `main` deploys the live site when listed in `live-branches`; a repo that deploys only on tags sets it empty. `testing` or `staging` deploys the test site; a repo with no test site leaves out `test`. Either way, `push:` lists every tag or branch that deploys.

### Secrets

Each site repo holds the same secret names. The values never appear in this repo.

| Secret | What it is |
|---|---|
| `DEPLOY_SSH_KEY` | The private deploy key. |
| `DEPLOY_HOST` | The live site's server address. |
| `DEPLOY_KNOWN_HOSTS` | The live server's public key, as one `known_hosts` line. |
| `TEST_DEPLOY_HOST` | The test site's server address. Only with a test site. |
| `TEST_DEPLOY_KNOWN_HOSTS` | The test server's public key. Only with a test site. |

A missing secret fails the run and names it.

### Versions

Site repos call `@v1`. A change that keeps every caller working moves the `v1` tag. A change that needs callers to change is `v2`, and each repo moves when it is ready.
