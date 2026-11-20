# Agentic Platform Lab

A four-week learning project (30 Nov – 27 Dec 2026). One public repo that shows hands-on experience with
agentic engineering on an AWS/EKS platform: agents opening and fixing PRs, spec-driven development,
agent evals, GitHub Actions with OIDC into AWS IAM, and DORA / DX Core 4 metrics.

The full intent, backlog and plan live in `docs/plan.md`. Read it when scope is unclear.

## Owner context

- The owner is a Staff Cloud Infrastructure Engineer: strong on Azure, AKS, Bicep, Entra ID, Azure DevOps,
  Kubernetes RBAC and FinOps. Newer to AWS (IAM, VPC, EKS, Terraform), GitHub Actions, .NET/C#, agent evals
  and Spec Kit. AWS is a deliberate learning goal: when an AWS concept maps to an Azure one (IAM role ≈ managed
  identity, EKS access entry ≈ AKS RBAC binding), say so briefly.
- Goal is learning plus a portfolio piece, not production hardening. Prefer the simplest thing that works
  and explain non-obvious choices in the PR description so the owner learns from them.

## Stack

- App: .NET 8 minimal API (`src/`), xUnit tests (`tests/`)
- Infra: Terraform (`infra/`) for EKS + ECR in a personal AWS account. `infra/bootstrap/` holds the long-lived
  parts (S3 state bucket, ECR, GitHub OIDC provider and deploy role); `infra/cluster/` holds the VPC and EKS
  cluster, created and destroyed per work session
- CI/CD: GitHub Actions (`.github/workflows/`), deploy via GitHub OIDC into an IAM role
- Agents: Claude Code GitHub Action (`@claude`), Dependabot
- Specs: GitHub Spec Kit (`specs/`)
- Evals: Braintrust or promptfoo (`evals/`), prompts in `prompts/`
- Docs: `docs/` (plan, notes, retro, platform write-up)

## How work flows

- Every piece of work is a GitHub issue (user story with acceptance criteria). Issues #1–#16 hold the backlog,
  grouped by milestone: Sprint 1 to Sprint 4.
- Start an issue: read it with `gh issue view <n>`, then create branch `feat/<n>-<short-slug>`
  (`chore/…` or `fix/…` where it fits).
- One issue per branch, one PR per issue. PR body starts with `Closes #<n>` and ticks the acceptance criteria met.
- Never commit directly to `main`. CI must be green before merge.
- Small, reviewable commits with conventional prefixes: `feat:`, `fix:`, `chore:`, `docs:`, `ci:`, `test:`.
- When an issue is finished, append a short entry to `docs/notes.md` (what was done, surprises, screenshots
  to take) — it feeds the blog post in Sprint 4.

## Hard rules

- No secrets in the repo, ever. AWS auth in Actions uses OIDC only (`permissions: id-token: write`,
  `aws-actions/configure-aws-credentials` with `role-to-assume` and `aws-region` as repo variables). No IAM
  access keys, in GitHub or locally: humans use IAM Identity Center (`aws sso login`).
- No content from the owner's employer (M-KOPA): no internal code, repo names, standards text, hostnames or
  screenshots. The AI.Skills material (issue #10) must be a generic rewrite.
- Personal AWS account only. EKS bills $0.10/h for the control plane and can't be stopped, so the cluster is
  ephemeral: `scripts/up.sh` before a session, `scripts/down.sh` after (Terraform apply/destroy of
  `infra/cluster`). Public subnets, no NAT gateway, one small node. Budget alert at $35/month.
- Don't add LangGraph, Temporal or multi-cluster setups: they're out of scope.

## Useful commands

```bash
gh issue list --milestone "Sprint 1"
gh issue view 2
dotnet test
aws sso login --profile lab
scripts/up.sh     # terraform apply infra/cluster
scripts/down.sh   # terraform destroy infra/cluster
```
