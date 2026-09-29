# Stats cards: how they work and how to keep them healthy

The two cards in the README are **live**: they are served by a self-hosted
[github-readme-stats](https://github.com/diogocouto18/github-readme-stats) instance on
Vercel (`github-readme-stats-diogocouto18.vercel.app`). The instance reads GitHub data with
a personal access token stored as the `PAT_1` environment variable on the Vercel project.
The images carry descriptive `alt` text.

The workflow [`stats-cards.yml`](../.github/workflows/stats-cards.yml) runs weekly (Mondays)
and:

1. fails if the PAT expiry date recorded in `.github/pat-expiry` is 21 days away or less;
2. requests both card URLs and fails on a non-200 response, a non-SVG response, or an
   error card (the server answers `200` with a "Something went wrong" card when the PAT is
   bad or expired).

A failed run emails the repository owner (GitHub notifications for failed workflow runs),
so a down host or an expired PAT is noticed before it shows on the profile. GitHub READMEs
cannot fall back to another image at runtime, so the cards are not replaced by static
snapshots: the choice is live data plus monitoring.

## PAT expiry

Check the token expiry at <https://github.com/settings/tokens> (fine-grained tokens:
<https://github.com/settings/personal-access-tokens>). Find the token that is set as
`PAT_1` on the Vercel project, then write its expiry date into `.github/pat-expiry`
as `YYYY-MM-DD`. From then on the weekly run fails from 21 days before expiry, which is
the renewal reminder.

**TODO (owner):** the current expiry date is not recorded yet. `.github/pat-expiry`
contains `TODO` until it is filled in.

## Rotating the token

1. Create a new token at the settings page above (classic token with `repo` and
   `read:user`, as described in the github-readme-stats docs).
2. Update `PAT_1` in the Vercel project settings and redeploy.
3. Update `.github/pat-expiry`, then run the workflow manually
   (Actions > Stats cards > Run workflow) to confirm it is green.
