# Memory Bank Workflows

Proven patterns for managing your Claude Code memory bank throughout the development lifecycle. These workflows keep your context lean, current, and actionable without manual overhead.

## Workflow Quick Reference

| Situation | Workflow | Command | Duration |
|-----------|----------|---------|----------|
| **Daily dev start** | [Delta Sync](#delta-sync) | `/context-diff` | 30 seconds |
| **Writing a PR** | [JIT Context](#jit-context) | `/context-query "path/**" --limit=8` | 15 seconds |
| **Production incident** | [Hotfix](#hotfix-context) | `/context-query "service/**" --limit=6 --format=inline` | 10 seconds |
| **Feature complete** | [Feature Wrap](#feature-wrap) | `/cleanup-context` | 1 minute |
| **Starting new work** | [Rehydrate](#new-feature-rehydrate) | `/rebuild-granular-from-archive` | 1 minute |
| **Weekly cleanup** | [Hygiene](#weekly-hygiene) | `/stale-check` | 2 minutes |

## Core Workflows

### Delta Sync
**Use when**: Starting your dev day or after pushing commits  
**Purpose**: Keep memory bank current without scanning entire repo

```bash
# Basic daily sync
/context-diff

# Sync specific branch changes  
/context-diff --since=origin/main

# Sync specific paths only
/context-diff --paths="src/auth/**,packages/web/**"
```

**What it does**: Updates only changed areas of your memory bank, maintaining token efficiency while keeping context fresh.

### JIT Context
**Use when**: Writing PRs, code reviews, or focused development  
**Purpose**: Generate minimal, precise context bundles

```bash
# Get context for a specific module
/context-query "src/payments/**" --limit=8 --format=bundle

# Focus on decisions and patterns only
/context-query "Feature: Checkout" --include=decisions,patterns --limit=5

# Inline context for immediate use
/context-query "auth system" --format=inline --limit=6
```

**What it does**: Creates surgical context slices - just what you need, nothing more.

### Hotfix Context
**Use when**: Production incidents, urgent fixes, security patches  
**Purpose**: Fastest possible context retrieval under pressure

```bash
# Emergency context for impacted service
/context-query "services/api/**" --include=decisions,patterns --limit=6 --format=inline

# Validate no stale guidance during incident
/stale-check --paths="services/api/**" --no-mark
```

**What it does**: Delivers critical context in seconds without loading entire memory bank.

### Feature Wrap
**Use when**: Completing milestones, major features, or releases  
**Purpose**: Archive current state for historical reference

```bash
# Create read-only snapshot
/cleanup-context

# Update memory bank first if needed
/update-memory-bank && /cleanup-context
```

**What it does**: Creates dated archive snapshots while keeping active memory bank lean.

### New Feature Rehydrate  
**Use when**: Starting work after archives, missing granular files  
**Purpose**: Restore granular context from archives efficiently

```bash
# Restore missing files from latest archive
/rebuild-granular-from-archive --strategy=summarize

# Target specific categories
/rebuild-granular-from-archive --paths="decisions/**,patterns/**" --limit-sections=20
```

**What it does**: Recreates focused granular files from archives, avoiding large context overhead.

### Weekly Hygiene
**Use when**: End of week, before releases, after major refactors  
**Purpose**: Remove stale content and validate memory bank accuracy

```bash
# Check for outdated content
/stale-check

# Focused stale check on recent changes
/stale-check --paths="src/**" --since=origin/main --no-mark

# Refresh if stale content found
/update-memory-bank
```

**What it does**: Identifies contradictory or outdated guidance, keeping memory bank trustworthy.

## Integration Patterns

### Daily Development Cycle
```bash
# Morning: sync overnight changes
/context-diff

# During work: get focused context
/context-query "src/feature/**" --limit=10

# End of day: quick hygiene check
/stale-check --no-mark
```

### Feature Development Cycle
```bash
# Start: ensure clean context
/rebuild-granular-from-archive --strategy=summarize

# During: maintain current context  
/context-diff --paths="src/feature/**"

# End: archive completed work
/cleanup-context
```

### Code Review Workflow
```bash
# Generate reviewer context
/context-query "changed-paths/**" --include=decisions,patterns --limit=8 --format=bundle

# Attach bundle to PR description or use inline for commit messages
```

## Troubleshooting

**Empty context results**: Broaden query terms or check if memory bank needs updating with `/update-memory-bank`

**Too much context**: Lower `--limit` parameter or use more specific path patterns

**Stale context warnings**: Run `/stale-check` to identify outdated content, then `/update-memory-bank` to refresh

**Missing granular files**: Use `/rebuild-granular-from-archive` to restore from latest archive

**Performance issues**: Use `--paths` parameter to limit scope, avoid scanning entire repository

## Best Practices

- **Be specific**: Use exact path patterns (`src/auth/**`) rather than broad terms
- **Limit scope**: Start with `--limit=5-10` and increase only if needed
- **Update frequently**: Run delta sync daily, full hygiene weekly
- **Archive milestones**: Use feature wrap after completing major work
- **Validate before incidents**: Never trust stale context during hotfixes

These workflows transform memory bank management from a manual chore into an automated part of your development flow, ensuring you always have the right context at the right time.