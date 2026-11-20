# Working notes

One short entry per finished issue: what was done, surprises, screenshots to take. Feeds the Sprint 4 blog post.

## #1 Repo process (Day 0, 19–20 Nov)

- Created repo, board, labels, Sprint 1–4 milestones and issues #1–#16 via the GitHub REST API.
- Added CLAUDE.md, docs/plan.md and a user-story issue form.
- Learned: the Claude GitHub app needs to be installed on the repo before it can write; GitHub Projects (v2) is GraphQL-only, so adding issues to the board is done in the UI or with `gh project item-add`.

- Configured the project board with `gh project`: Status options, a 2-week Sprint iteration field (first sprint starts 30 Nov, after vacation), a Size field, and an auto-add workflow for new issues.
- Added a PR template (`Closes #<n>` + acceptance criteria checklist) and ignored the local `.nodeterm/` folder.
- Surprise: `gh` needs the extra `project` scope (`gh auth refresh -s project`) before any `gh project` command works.
- Screenshots to take: board view grouped by Sprint, the iteration field settings, the auto-add workflow, and the `main` ruleset once it exists (step 20).