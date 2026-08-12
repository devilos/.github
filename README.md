# .github

Org-level defaults for [Devilos](https://github.com/Devilos). This repo holds
shared CI, not product code.

It is **public on purpose**: GitHub only runs a ruleset-required workflow on
repositories whose visibility the hosting repo can reach — a private host would
cover only the org's private repos and silently skip the public ones
([dealter](https://github.com/Devilos/dealter),
[debait](https://github.com/Devilos/debait)). Public is the only setting that
reaches all of them. The workflows here reference secrets by name but contain no
secret values.

## Workflows

| Workflow | What it does |
| -------- | ------------- |
| [org-tests.yml](.github/workflows/org-tests.yml) | Detects each repo's package manager, pinned Node version, and available scripts, then runs `test`, falls back to `compile`, or skips if the repo has neither. Never runs e2e. |
| [org-claude-code-review.yml](.github/workflows/org-claude-code-review.yml) | Runs a Claude Code review on every pull request. |

Neither is copied into individual repos. Both are applied org-wide through the
**Protect default branch** ruleset (org Settings → Repository → Rulesets →
*Require workflows to pass before merging*), which means they also cover any repo
created in future with no extra setup.

Because ruleset-required workflows only support the `pull_request`,
`pull_request_target`, and `merge_group` events, these are standalone workflows
rather than reusable `workflow_call` ones, and they run on pull requests only.

## Required configuration

`CLAUDE_CODE_OAUTH_TOKEN` must exist as an **organization secret** shared with
every repo. Until it does, the review job logs a warning and skips rather than
failing — these workflows gate merges, so a missing secret must never block every
pull request in the org.
