# .github

Shared reusable workflows, security scanning configuration, and starter workflow templates for all adamcongdon repositories.

## Reusable workflows (called from any repo)

- `gitleaks-scan.yml` — Secret scanning via Gitleaks
- `semgrep-scan.yml` — SAST scanning via Semgrep (multi-language)

Add a thin caller workflow to any repo:

```yaml
name: Security
on: [push, pull_request]
jobs:
  gitleaks:
    uses: adamcongdon/.github/.github/workflows/gitleaks-scan.yml@main
  semgrep:
    uses: adamcongdon/.github/.github/workflows/semgrep-scan.yml@main
```

## Starter workflow templates (appear in the GitHub UI's "New workflow" picker)

Three Claude-powered automation workflows. When you click **Actions → New workflow** on any of your repos, these show up under the "By adamcongdon" suggested category:

- **Claude Code (@mention handler)** — `claude.yml`. Reacts to `@claude` mentions across issues, issue comments, PR review comments, and PR reviews.
- **Claude Code Review** — `claude-code-review.yml`. Auto-reviews every PR via the `code-review` plugin.
- **Claude Issue Triage** — `claude-triage.yml`. On every new issue, posts an `@claude` triage request that applies type/priority/component labels + root-cause hypothesis + suggested fix + effort estimate.

### Required secrets per repo

| Secret | Used by | How to set |
|---|---|---|
| `CLAUDE_CODE_OAUTH_TOKEN` | all three workflows | `claude setup-token` locally, then `gh secret set CLAUDE_CODE_OAUTH_TOKEN -R <owner/repo>` |
| `TRIAGE_PAT` | `claude-triage.yml` only | Fine-grained PAT on the target repo, Issues = Read/Write, Metadata = Read. `gh secret set TRIAGE_PAT -R <owner/repo>`. **Required** because `GITHUB_TOKEN`-authored comments do not fire downstream `issue_comment` events. |

### Optional: seed component labels

```bash
gh label create core ui api build ci docs -R <owner/repo>
```

### Automated installer (alternative to UI)

For batch installs or per-project customization (project name, description, components, language-specific tool allowlist), use the installer script:

```bash
bun ~/.claude/PAI/Tools/InstallClaudeActions.ts <owner/repo> \
  --project-name "My Project" \
  --project-description "One-paragraph description used in the triage prompt." \
  --components hook,scrubber,judge,web,server,cli \
  --language ts \
  --triage-pat-secret-name MYREPO_TRIAGE_PAT
```

Run `bun ~/.claude/PAI/Tools/InstallClaudeActions.ts --help` for the full flag list.
