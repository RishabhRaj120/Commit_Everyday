# Commit_Everyday

A daily streak auto commit for github for the lazy people out there

## About

Commit_Everyday uses GitHub Actions to create one configurable daily commit on the repository's default branch. It runs in GitHub's infrastructure, so your computer does not need to be on.

## Features

- Automatic daily commits from 00:15 IST, with retries every five minutes through 00:55 IST.
- Customizable commit messages.
- Automatic day counter.
- Automatic counter reset when the statement changes.
- Persistent streak state.
- Duplicate prevention.
- GitHub Actions automation.
- Custom Git author attribution.
- Manual workflow execution.

## Customizing Your Commit Message

Edit only [`.github/commit-config.json`](.github/commit-config.json) in GitHub and commit your change. For example:

```json
{
  "statement": "life is good"
}
```

The statement must be a single, non-empty line. Changing it resets the counter to day 1 on the next successful daily execution. You never need to edit the workflow to change the message.

## Commit Message Format

```text
<statement>_day_<number>_<date>_<time>
```

For example: `life_is_good_day_1_2026-11-15_00:15`. Spaces in the configured statement are converted to underscores in the commit message.

## How It Works

1. From 00:15 through 00:55 in `Asia/Kolkata`, GitHub Actions gets a run opportunity every five minutes. The first successful run for an IST date creates the commit; later runs skip. You can also start the same workflow manually.
2. The workflow reads and validates `.github/commit-config.json`.
3. It reads `.github/streak-state.json` and `.github/daily-activity.md` to prevent a second commit for the current IST date.
4. If the configured statement matches the active statement, the counter increases by one; otherwise it starts at day 1.
5. The workflow writes the new state and appends one IST timestamped activity record, commits only those two files, and pushes to the default branch. It uses the captured IST execution timestamp for both Git author and committer dates, including the `+05:30` offset.

## Setup Instructions

1. In **Settings → Actions → General**, set **Workflow permissions** to **Read and write permissions** and allow GitHub Actions to create and approve pull requests only if your repository policy requires it.
2. In **Settings → Secrets and variables → Actions**, create these repository secrets:
   - `GH_COMMIT_NAME` — the name shown as the Git author and committer.
   - `GH_COMMIT_EMAIL` — an email address verified on your GitHub account. A GitHub-provided noreply email is a good privacy-preserving choice.
3. Ensure the workflow file remains on the default branch and Actions are enabled for the repository.

To find a noreply address, open **GitHub Settings → Emails** and use the address shown under **Keep my email addresses private**. Add that exact address as `GH_COMMIT_EMAIL` so commits can be attributed to your account.

## Manual Execution

Go to **Repository → Actions → Daily Commit → Run workflow** and run it from the default branch. Manual runs use the same safeguards, so they cannot create a second commit for the same IST date.

## Troubleshooting

- **Missing secrets:** Add both `GH_COMMIT_NAME` and `GH_COMMIT_EMAIL`; the workflow stops with a clear error until both exist.
- **Push permission denied:** Confirm the repository’s Actions workflow permission is set to read and write, and that branch protection permits the GitHub Actions token to push.
- **Scheduled workflow did not run:** Confirm Actions are enabled and that the workflow is on the default branch. Public repositories with no activity for 60 days can have scheduled workflows disabled by GitHub.
- **Commit is not attributed to your profile:** Use an email verified on the intended GitHub account, preferably its GitHub noreply address.
- **A manual run made no commit:** A commit or log entry already exists for the current IST date. This is intentional duplicate prevention.

## Limitations

GitHub may delay scheduled workflows during periods of high load, so the recorded time is the actual execution time in IST rather than an assumed `00:15`. GitHub Actions availability and repository settings also control whether a scheduled run can start. Missed schedules are not recreated as backdated commits; the next successful run continues the counter once.
