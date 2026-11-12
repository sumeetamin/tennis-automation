# Speed Runners — public automation

This repository contains **workflow definitions only**. The sports code, models, datasets and generated feeds stay in the private `sumeetamin/tennis` repository.

## Data flow

1. A scheduled/manual workflow checks out private `sumeetamin/tennis` using `TENNIS_REPO_TOKEN`.
2. It runs the existing private scripts on GitHub-hosted Linux runners.
3. The scripts publish only the generated JSON feeds back to the private repo.
4. The Vercel app reads those feeds through its existing authenticated GitHub data route (`GITHUB_DATA_TOKEN` remains in Vercel).

No Vercel deploy hook is needed for feed updates: the Vercel API reads the current private feed when requested.

## Configure before enabling

Add these under **Settings → Secrets and variables → Actions → Secrets** in this repository. Re-enter the existing values privately; GitHub cannot copy secrets between repositories.

- `TENNIS_REPO_TOKEN`: fine-grained PAT restricted to `sumeetamin/tennis`, Contents read/write. Used to fetch code and publish feed JSON.
- `ODDS_API_KEYS` (and optional `ODDS_API_KEY`): Odds API key pool.
- `TENNIS_RESULTS_API_KEYS` (and optional `TENNIS_RESULTS_API_KEY`): tennis results API key pool.
- `NBA_REPOS_READ_TOKEN`: read-only access to `nba-betting-data` and `nba-betting-model`.
- `MMA_REPOS_READ_TOKEN`: read-only access to `mma-dataset` and `MMA-`.
- `ODDSPAPI_API_KEY`: volleyball odds key.

Optional Actions variable: `VOLLEYBALL_ODDS_BOOKMAKER`.

Add the secrets above, then disable the matching scheduled workflows in the private repo to prevent duplicate runs. Set Actions variable `PUBLIC_RUNNER_ENABLED` to `true`, manually run each workflow once, and verify its private feed update and the signed-in Vercel page. Keep the private workflows disabled after the checks pass.

## Security

- Workflow files and run logs in this repository are public. They must never print picks, odds responses, private data, or secrets.
- Secret-bearing workflows accept only schedule/manual triggers; they do not run on pull requests or forks.
- Keep write access limited to the owner. Do not merge third-party workflow changes without reviewing them.
- The workflow token can write feed JSON to the private tennis repository. A malicious workflow change could misuse any secret, so review every change before it runs.
- This design protects the model/data source repository from public browsing; it cannot hide the automation logic or make public logs private.
