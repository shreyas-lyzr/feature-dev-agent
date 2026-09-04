# feature-dev-agent

A software-engineering [OpenGAP](https://github.com/gitagent-protocol) agent. Give it a repo and a feature; it investigates the code, posts a plan with acceptance criteria and the files it will change, waits for your approval, then builds the feature and opens a pull request.

Driven via [gitagent](https://github.com/gitagent/gitagent) / gitclaw from `@computeragent` infrastructure (Slack bot, or the `/run`, `/tasks`, `/sandboxes` endpoints of a ComputerAgent harness server). Sibling of [general-agent](https://github.com/shreyas-lyzr/general-agent).

## Workflow

1. **Intake** — asks for the target repo and the feature if either is missing.
2. **Investigate** — clones the repo, reads the code paths it would touch, learns the test and lint setup.
3. **Plan** — posts summary, assumptions, acceptance criteria, files to change, test plan, risks, and any open questions.
4. **Confirm** — waits. Revises the plan on feedback. Builds only on an explicit go-ahead.
5. **Build** — feature branch, implementation, tests, repo's own checks.
6. **PR** — opens a pull request against the base branch with acceptance criteria as a verified checklist, and replies with the link.

It never pushes to the default branch, never force-pushes, and never merges.

## Files

- `agent.yaml` — GAP manifest (model, runtime)
- `SOUL.md` — system prompt: identity, the phased workflow, engineering standards, guardrails
- `memory/` — persistent memory (managed by the harness)

## Environment

- `ANTHROPIC_API_KEY` — model access
- `GITHUB_TOKEN` (mirrored as `GH_TOKEN`) — read/write on the repos it should work in

## Usage

```bash
# As a one-shot task via the ComputerAgent SDK
runTask({
  source: { kind: "github", repo: "shreyas-lyzr/feature-dev-agent" },
  harness: "gitagent",
  envs: {
    ANTHROPIC_API_KEY: process.env.ANTHROPIC_API_KEY,
    GITHUB_TOKEN: process.env.GITHUB_TOKEN,
  },
  message: "Repo: acme/billing. Feature: add a CSV export button to the invoices page.",
});
```
