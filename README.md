# .github

Shared reusable workflows and security scanning configuration for all adamcongdon repositories.

## Reusable Workflows

- `gitleaks-scan.yml` - Secret scanning via Gitleaks
- `semgrep-scan.yml` - SAST scanning via Semgrep (multi-language)

## Usage

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
