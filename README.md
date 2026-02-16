# Codebase Health Check Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Codebase%20Health%20Check-blue?logo=github)](https://github.com/marketplace/actions/codebase-health-check)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> Automated codebase health scoring for every pull request. Get a health report card with scores across 5 dimensions — posted directly as a PR comment.

## What It Does

On every pull request, this action analyzes your repository and posts a health report as a comment:

```
🟢 Codebase Health: 78/100 (Grade B)

| Dimension          | Score | Max | Status |
|--------------------|-------|-----|--------|
| Code Complexity    | 20    | 25  | ✅     |
| Documentation      | 14    | 20  | ✅     |
| Test Coverage      | 16    | 20  | ✅     |
| Dependency Health   | 10    | 15  | ✅     |
| Git & Collaboration | 18    | 20  | ✅     |

### Recommendations
- Consider breaking down files over 500 lines in src/
- Add docstrings to 12 public functions missing documentation
```

## Quick Start

Add to `.github/workflows/health-check.yml`:

```yaml
name: Codebase Health

on:
  pull_request:
    branches: [main]

permissions:
  pull-requests: write

jobs:
  health-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for git analysis

      - uses: glue-tools-ai/codebase-health-action@v1
        with:
          comment-on-pr: 'true'
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `path` | Repository path to analyze | `.` |
| `fail-threshold` | Minimum score to pass (0 = never fail) | `0` |
| `comment-on-pr` | Post report as PR comment | `true` |
| `format` | Output format: `markdown`, `json` | `markdown` |
| `include-recommendations` | Include improvement tips | `true` |

## Outputs

| Output | Description |
|--------|-------------|
| `score` | Overall health score (0-100) |
| `grade` | Letter grade (A-F) |
| `complexity-score` | Code complexity score (0-25) |
| `documentation-score` | Documentation score (0-20) |
| `test-score` | Test coverage score (0-20) |
| `dependency-score` | Dependency health score (0-15) |
| `git-score` | Git collaboration score (0-20) |
| `report` | Full report in specified format |

## Advanced Usage

### Fail CI Below a Threshold

```yaml
- uses: glue-tools-ai/codebase-health-action@v1
  with:
    fail-threshold: '60'  # Fail if score drops below 60
```

### Use Score in Subsequent Steps

```yaml
- uses: glue-tools-ai/codebase-health-action@v1
  id: health

- name: Check results
  run: |
    echo "Score: ${{ steps.health.outputs.score }}"
    echo "Grade: ${{ steps.health.outputs.grade }}"
```

### JSON Output for Custom Processing

```yaml
- uses: glue-tools-ai/codebase-health-action@v1
  id: health
  with:
    format: 'json'
    comment-on-pr: 'false'

- name: Process results
  run: echo '${{ steps.health.outputs.report }}' | jq '.dimensions'
```

## Scoring Dimensions

| Dimension | Max Score | What It Measures |
|-----------|-----------|------------------|
| **Code Complexity** | 25 | File sizes, directory depth, structural complexity |
| **Documentation** | 20 | README quality, inline comments, docstring coverage |
| **Test Coverage** | 20 | Test file presence, test-to-source ratio, CI/CD config |
| **Dependency Health** | 15 | Lock file status, dependency count, freshness |
| **Git & Collaboration** | 20 | Commit frequency, contributor diversity, bus factor |

## Grade Scale

| Grade | Score Range | Meaning |
|-------|------------|---------|
| A | 90-100 | Excellent — well-maintained, low risk |
| B | 80-89 | Good — minor improvements possible |
| C | 70-79 | Fair — some areas need attention |
| D | 60-69 | Poor — significant debt accumulating |
| F | 0-59 | Critical — major refactoring needed |

## Built By

Built by [Glue](https://getglueapp.com) — AI codebase intelligence for product teams.

This action uses [codebase-health-score](https://github.com/glue-tools-ai/codebase-health-score), our open-source repository health analyzer. For continuous monitoring with deeper analysis including feature discovery, tribal knowledge extraction, and natural language codebase querying, check out [Glue](https://getglueapp.com).

## License

MIT License — Copyright 2026 Glue
