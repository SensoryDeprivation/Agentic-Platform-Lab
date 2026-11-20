# Agentic Platform Lab

A four-week learning project (23 Nov – 20 Dec 2026). One public repo that shows hands-on experience with
agentic engineering on an Azure/AKS platform: agents opening and fixing PRs, spec-driven development,
agent evals, GitHub Actions with Entra workload identity, and DORA / DX Core 4 metrics.

The full intent, backlog and plan live in `docs/plan.md`. Read it when scope is unclear.

## Owner context

- The owner is a Staff Cloud Infrastructure Engineer: strong on Azure, AKS, Bicep, Entra ID, Azure DevOps,
  Kubernetes RBAC and FinOps. Newer to GitHub Actions, .NET/C#, agent evals and Spec Kit.
- Goal is learning plus a portfolio piece, not production hardening. Prefer the simplest thing that works
  and explain non-obvious choices in the PR description so the owner learns from them.

## Stack

- App: .NET 8 minimal API (`src/`), xUnit tests (`tests/`)
- Infra: Bicep (`infra/`) for AKS + ACR in a personal Azure subscription
- CI/CD: GitHub Actions (`.github/workflows/`), deploy via OIDC / Entra workload identity federation
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

- No secrets in the repo, ever. Azure auth in Actions uses OIDC only (`permissions: id-token: write`,
  `azure/login@v2` with client-id / tenant-id / subscription-id as repo variables).
- No content from the owner's employer (M-KOPA): no internal code, repo names, standards text, hostnames or
  screenshots. The AI.Skills material (issue #10) must be a generic rewrite.
- Personal Azure subscription only. Keep costs low: one small node, Free tier control plane, stop the cluster
  when not in use (`scripts/aks-stop.sh`).
- Don't add LangGraph, Temporal or multi-cluster setups: they're out of scope.

## Useful commands

```bash
gh issue list --milestone "Sprint 1"
gh issue view 2
dotnet test
az deployment group create -g <rg> -f infra/main.bicep
```
