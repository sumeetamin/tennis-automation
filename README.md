# Speed Runners — public automation

Public GitHub Actions runners for the private sports projects. This repo contains workflow definitions only; it does not store model code, datasets, predictions, or API keys.

## Data flow

- Workflows check out the private source repos using Actions secrets, run their existing scripts, and commit generated feeds back to those private repos.
- The tennis Vercel app reads current feed JSON through its authenticated GitHub data route. Vercel's `GITHUB_DATA_TOKEN` stays in Vercel.
- Football writes the board/history to private `football-match-lab`; its Vercel project continues serving that project's feed.
- No decrypted model, predictions, or API response is uploaded as a public artifact.

## Secrets to add here

Use **Settings → Secrets and variables → Actions → Secrets**. Re-enter the existing values privately; GitHub cannot copy secrets between repositories.

- `TENNIS_REPO_TOKEN`: fine-grained PAT restricted to `sumeetamin/tennis`, Contents read/write.
- `FOOTBALL_REPO_TOKEN`: fine-grained PAT restricted to `sumeetamin/football-match-lab`, Contents read/write.

For website manual refresh buttons, the Vercel Production variables `GITHUB_WORKFLOW_TOKEN` and `FOOTBALL_WORKFLOW_TOKEN` must be fine-grained PATs restricted to `tennis-automation`, with Actions read/write. The same token may be used for both.
- `ODDS_API_KEYS` (optional `ODDS_API_KEY`): Odds API key pool.
- `TENNIS_RESULTS_API_KEYS` (optional `TENNIS_RESULTS_API_KEY`): tennis results API key pool.
- `NBA_REPOS_READ_TOKEN`: read-only access to `nba-betting-data` and `nba-betting-model`.
- `MMA_REPOS_READ_TOKEN`: read-only access to `mma-dataset` and `MMA-`.
- `ODDSPAPI_API_KEY`: volleyball odds key.
- `APIFOOTBALL_KEY`: optional key for the weekly Football roster workflow.
- `FOOTBALL_DATA_PERMISSION_REFERENCE`: optional, only for the manual restricted-market historical-data task.

Optional Actions variable: `VOLLEYBALL_ODDS_BOOKMAKER`. Keep `PUBLIC_RUNNER_ENABLED` unset until secrets are configured and you are ready to cut over.

## Cutover

1. Add the secrets above.
2. Disable the matching scheduled workflows in the private tennis and Football repos, to prevent duplicate runs.
3. Set Actions variable `PUBLIC_RUNNER_ENABLED` to `true`.
4. Manually run each sports workflow once. Check the private feed updates and the signed-in Vercel pages.
5. Keep the old scheduled workflows disabled after verification.

The tennis app routes now target workflows in this repository. Update the Vercel Production tokens above before using the website's manual refresh buttons. Football's Vercel integration remains connected to the private Football repo.

## Workflows

Tennis predictions/results; basketball and volleyball boards/results; Football board/results and roster refresh; MMA board/history; hourly site health; manual Football model retraining, evaluation, and data maintenance.

## Security and limits

- Workflow YAML and GitHub Actions logs are public. Logs suppress private script output; never add a step that prints picks, odds payloads, private data, or secrets.
- Secret-bearing workflows run only on schedules or manual dispatch—never pull requests/forks.
- Keep write access limited to you and review every workflow change. A malicious workflow change could misuse its secrets.
- Standard GitHub-hosted runners are free for public repositories. This does not make logs, workflow logic, or outputs public-safe by itself.
