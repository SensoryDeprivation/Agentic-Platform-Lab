# Agentic Platform Lab — Intent & Plan

Live version: https://claude.ai/artifact/JgzfCHQzMbK3hXMVNLhn9y (this file is a snapshot from 20 Nov 2026; the live version predates the switch to AWS).

## Intent

By 27 Dec 2026, this public repo shows working, hands-on experience with the agentic-engineering skills that
Platform Engineering Manager roles ask for. A hiring manager can click through it in five minutes.

**Success looks like:**

- A .NET API running on AWS EKS, deployed by GitHub Actions with OIDC (no secrets in the repo), with the
  cluster defined in Terraform and rebuilt from scratch each work session
- An agent that fixes a red CI run and opens a PR, plus one that turns a Dependabot advisory into a fix PR
- One feature built end to end with GitHub Spec Kit (spec, plan, tasks, implement)
- An eval suite of 15+ golden cases with an LLM judge, run in CI
- DORA-style metrics for the repo and a one-page platform write-up mapped to DX Core 4
- One blog post or LinkedIn article linking to the repo

**Non-goals:** production-grade hardening, multi-cluster setups, LangGraph or Temporal, becoming a C# developer.

**Why AWS, not Azure:** the owner already knows Azure/AKS well; AWS is a deliberate second learning goal. The
agent, spec and eval work in Sprints 2–3 is cloud-agnostic, so the switch only changes Sprint 1.

**Budget:** 8–10 hours a week, under €30 of AWS spend (new-account credits may cover it), roughly €10–20 of API spend.

## How we work in GitHub

| GitHub feature | Use |
| --- | --- |
| Issues | One user story per issue, acceptance criteria as a checklist (#1–#16) |
| Labels | `type:story` / `type:chore` / `type:bug` plus one `epic:*` per epic |
| Milestones | Sprint 1–4, one per week |
| Project board | Backlog → Ready → In progress → In review → Done; fields Sprint (iteration), Size (S/M/L) |
| Branch ruleset | `main` protected: PR required, status checks pass, no force pushes |
| Pull requests | Branch `feat/<issue>-<slug>`, body `Closes #<issue>` |
| Actions | Build/test on PR, deploy on merge, agent workflows |
| Releases | Tag `v0.1.0`, `v0.2.0`… at the end of each sprint |

**Weekly rhythm:** Monday 15 min plan (pull issues into Ready) · build during the week · Sunday 20 min review
(check the gate, cut a release, three lines in `docs/retro.md`, close the milestone).

**Working with agents:** from Sprint 2, hand at least one issue per sprint to `@claude` and review its PR like a
colleague's.

## Sprints and gates

| Sprint | Dates | Theme | Issues | Gate (don't start the next sprint until met) |
| --- | --- | --- | --- | --- |
| 1 | 30 Nov – 6 Dec | Foundation | #1–#5 | API live on EKS, deployed via OIDC |
| 2 | 7–13 Dec | Agentic dev | #6–#10 | An agent-authored PR merged |
| 3 | 14–20 Dec | Specs and evals | #11–#14 | Evals gate prompt changes in CI |
| 4 | 21–27 Dec | Metrics and story | #15–#16 | Repo and blog post public |

Sprints 2 and 3 cover the real gaps (agents and evals). If time runs short, cut from Sprint 4, not from those.

**AWS timebox:** if EKS isn't serving `/health` by Sun 6 Dec, deploy to `kind` locally (or ECS Fargate), note
it in the retro and start Sprint 2 anyway. Finish EKS later rather than let it eat into agents and evals.

## Backlog

| # | Epic | Story |
| --- | --- | --- |
| 1 | Foundation | Repo process: board, labels, templates, protected main |
| 2 | Foundation | Minimal .NET 8 API with /health and tests |
| 3 | Foundation | Build and test every PR with GitHub Actions |
| 4 | Foundation | EKS and ECR defined in Terraform (ephemeral cluster) |
| 5 | Foundation | Deploy to EKS via GitHub OIDC into an IAM role |
| 6 | Agentic dev | @claude on an issue opens a PR |
| 7 | Agentic dev | Red CI triggers an agent fix PR |
| 8 | Agentic dev | Security advisory starts an agent fix |
| 9 | Agentic dev | Agent reviewer on every PR |
| 10 | Agentic dev | Public subset of AI.Skills plugin in the repo |
| 11 | Spec-driven | Build /todos feature with GitHub Spec Kit |
| 12 | Evals | 15+ golden eval cases |
| 13 | Evals | LLM-judge rubric in Braintrust or promptfoo |
| 14 | Evals | Evals run in CI on prompt changes |
| 15 | Metrics | DORA metrics and DX Core 4 one-pager |
| 16 | Metrics | README, demo GIF and blog post |

Full user stories and acceptance criteria are in the GitHub issues.

## Setup status (Day 0)

- [x] Repo created
- [x] Project board created
- [x] Labels, Sprint 1–4 milestones and issues #1–#16 created
- [x] Add issues #1–#16 to the board
- [x] Board: add Backlog / Ready / In review to Status; add Sprint (iteration) and Size fields; enable Auto-add
- [x] Ruleset on `main`: require PR, block force pushes and deletions (status checks added with #3)
- [ ] AWS account: root MFA, IAM Identity Center admin user, `aws sso login` profile, $35 budget alert
- [ ] Sprint 2: run `/install-github-app` in Claude Code to install the Claude GitHub Action

**OIDC in one paragraph (issue #5):** in `infra/bootstrap`, create an IAM OIDC identity provider for
`token.actions.githubusercontent.com` (audience `sts.amazonaws.com`) and an IAM role whose trust policy only
accepts subject `repo:SensoryDeprivation/Agentic-Platform-Lab:ref:refs/heads/main` (the match is case-sensitive,
so use the repo's exact casing). Give the role ECR push rights, and in `infra/cluster` an EKS access entry so it
can deploy. In the workflow set `permissions: id-token: write` and use `aws-actions/configure-aws-credentials`
with `role-to-assume` and `aws-region` (repo variables, not secrets). Azure equivalent: Entra app registration +
federated credential + AcrPush and AKS RBAC roles.

## Risks

| Risk | Mitigation |
| --- | --- |
| Employer IP (AI.Skills, internal standards) | Only a generic rewrite, or written OK first. Never copy internal content |
| Employer cloud account used by mistake | Personal AWS account only; check `aws sts get-caller-identity` before any apply |
| EKS cost ($0.10/h control plane, can't be stopped) | Ephemeral cluster via `up.sh`/`down.sh`, public subnets, no NAT, one small node; $35 budget alert; fallback `kind` locally |
| AWS learning curve eats Sprint 1 | Timebox: `kind` or ECS fallback if EKS isn't live by 6 Dec |
| API cost | Spending limit on the API key |
| Slippage | Protect Sprints 2–3; cut Sprint 4 scope first, never skip the write-up |
| Overclaiming in interviews | "I built this to learn X; here's what I'd do differently at team scale" |
