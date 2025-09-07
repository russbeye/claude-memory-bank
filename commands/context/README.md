# Context Commands

Essential commands for managing your Claude Code memory bank efficiently. These commands keep your project context current, focused, and immediately useful without manual overhead.

## Quick Command Reference

| When You Need | Command | Use Case | Speed |
|---------------|---------|----------|-------|
| **Focused context** | `/context-query "path/**" --limit=10` | Development work, PR reviews | 15 sec |
| **Stay current** | `/context-diff` | Daily sync, after commits | 30 sec |
| **Full refresh** | `/update-memory-bank` | Major changes, initial setup | 2 min |
| **Check accuracy** | `/stale-context-check` | Weekly hygiene, after refactoring | 1 min |
| **Archive work** | `/cleanup-context` | Feature completion, milestones | 1 min |
| **Restore files** | `/rebuild-granular-from-archive` | Missing context, post-archive | 1 min |

## Core Commands

### `/context-query` - Get Targeted Context
**Use for**: Development work, PR reviews, focused research

```bash
# Get context for specific module  
/context-query "src/auth/**" --limit=8

# Focus on decisions and patterns only
/context-query "Feature: Checkout" --include=decisions,patterns --limit=5

# Inline context for immediate use
/context-query "payment processing" --format=inline --limit=6

# Generate reviewable bundle
/context-query "src/api/**" --format=bundle --limit=12
```

**What it does**: Creates surgical context slices containing exactly what you need, nothing more. Perfect for staying focused without context overload.

### `/context-diff` - Delta Sync  
**Use for**: Daily development, staying current with changes

```bash
# Basic daily sync
/context-diff

# Sync specific branch changes
/context-diff --since=origin/main  

# Sync only certain paths
/context-diff --paths="src/auth/**,config/**"

# Preview what would change
/context-diff --dry-run
```

**What it does**: Updates only changed areas of your memory bank using git diffs. Token-efficient way to keep context current without scanning entire repository.

### `/update-memory-bank` - Full Refresh
**Use for**: Initial setup, major refactoring, comprehensive updates

```bash
# Full memory bank refresh
/update-memory-bank

# Focus on specific areas (if supported)
/update-memory-bank --paths="src/services/**"
```

**What it does**: Completely refreshes memory bank from current codebase state. Creates missing files, updates existing ones, ensures everything reflects current reality.

### `/stale-context-check` - Validate Accuracy
**Use for**: Weekly hygiene, post-refactoring validation, quality assurance

```bash
# Check for outdated content
/stale-context-check

# Report-only mode (no markers)
/stale-context-check --no-mark

# Check specific areas since baseline
/stale-context-check --paths="src/**" --since=origin/main
```

**What it does**: Identifies contradictory, obsolete, or missing references in your memory bank. Marks suspect sections and generates actionable reports.

### `/cleanup-context` - Archive Completed Work
**Use for**: Feature completion, milestones, releases

```bash
# Create dated archive snapshot
/cleanup-context
```

**What it does**: Creates read-only historical snapshots while keeping active memory bank lean. Archives preserve history without cluttering daily context.

### `/rebuild-granular-from-archive` - Restore Context
**Use for**: Missing files after archives, team onboarding, context restoration

```bash
# Restore missing files from latest archive  
/rebuild-granular-from-archive --strategy=summarize

# Target specific categories
/rebuild-granular-from-archive --paths="decisions/**,patterns/**" --limit-sections=20
```

**What it does**: Recreates focused memory bank files from archive snapshots. Useful when starting new work or when granular files are missing.

## Daily Workflow Integration

### Morning Routine
```bash
# Sync overnight changes
/context-diff

# Get context for today's work area
/context-query "src/feature/**" --limit=10
```

### During Development
```bash
# Quick context for new area
/context-query "specific-module/**" --format=inline --limit=5

# Stay current as you work
/context-diff --paths="areas-you're-changing/**"
```

### End of Feature/Day
```bash
# Update memory bank with your changes
/update-memory-bank

# Archive if completing major work
/cleanup-context
```

### Weekly Maintenance
```bash
# Check for outdated guidance
/stale-context-check --no-mark

# Fix any issues found
/update-memory-bank
```

## Common Patterns

### Code Review Workflow
```bash
# Generate context for reviewers
/context-query "changed-files/**" --include=decisions,patterns --limit=8 --format=bundle

# Validate current accuracy
/stale-context-check --paths="review-area/**" --no-mark
```

### Feature Development Cycle
```bash
# Start: Get existing context
/context-query "Feature: YourFeature" --include=decisions,architecture

# During: Stay current
/context-diff --paths="feature-area/**"

# End: Update and archive
/update-memory-bank && /cleanup-context
```

### Incident Response
```bash
# Emergency context for affected service
/context-query "services/problematic-service/**" --format=inline --limit=6

# Validate no stale troubleshooting info
/stale-context-check --paths="services/problematic-service/**" --no-mark
```

## Troubleshooting

**No context results**: Broaden search terms or run `/update-memory-bank` to refresh content

**Too much context**: Use `--limit=5-8` and more specific path patterns like `src/specific-module/**`

**Stale warnings**: Run `/stale-context-check` to identify issues, then `/update-memory-bank` to refresh

**Missing files**: Use `/rebuild-granular-from-archive` to restore from latest snapshot

**Slow performance**: Use `--paths` filters to limit scope; avoid scanning entire repository

## Best Practices

### Query Optimization
- **Be specific**: Use `src/auth/**` instead of just `auth`
- **Limit scope**: Start with `--limit=5-10`, increase only if needed
- **Use formats**: `--format=inline` for immediate use, `--format=bundle` for reusable context

### Sync Efficiency  
- **Daily delta sync**: Run `/context-diff` daily rather than letting changes accumulate
- **Path filtering**: Use `--paths` when working on specific areas
- **Dry run first**: Use `--dry-run` to preview changes before applying

### Maintenance Discipline
- **Regular hygiene**: Run `/stale-context-check` weekly
- **Archive milestones**: Use `/cleanup-context` after completing features
- **Validate before critical work**: Never trust stale context during incidents

### Integration Tips
- **Combine commands**: Chain operations like `/update-memory-bank && /cleanup-context`
- **Team coordination**: Share context bundles for code reviews
- **Automate routine**: Consider hooking context-diff into your git workflow

These commands transform memory bank management from manual maintenance into an automated part of your development flow, ensuring you always have accurate, focused context when you need it.