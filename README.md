# Claude Code Memory Bank Extension

A structured context management system for Claude Code that provides intelligent memory banking, specialized agents, and development workflows to enhance your coding sessions with persistent, queryable project knowledge.

## What This Provides

**Intelligent Context Management**: Instead of losing project context between Claude sessions, this system maintains structured memory banks of decisions, patterns, architecture, and troubleshooting knowledge that persist and evolve with your codebase.

**Specialized Agents**: Purpose-built AI agents for specific tasks like code searching, memory synchronization, UX design, and context analysis that work more efficiently than generic prompts.

**Development Workflows**: Proven patterns for feature development, hotfixes, context cleanup, and project hygiene that scale with your team.

## Quick Start

1. **Clone this repository**:
   ```bash
   git clone git@github.com:russbeye/claude-memory-bank.git
   cd claude-memory-bank
   ```

2. **Copy the system files to your Claude directory**:
   ```bash
   cp -r {agents,commands,workflows} ~/.claude/
   cp CLAUDE.md ~/.claude/
   # settings.json contains suggested permissions
   ```

3. **Sync your first memory bank**:
   ```bash
   /update-memory-bank
   # Will be put into `project/.claude/memory-bank`
   ```

4. **Start with context discovery**:
   ```bash
   /context-query "src/**" --limit=10
   ```

That's it. Claude now has persistent, queryable knowledge about your project.

## Core Components

### Memory Bank System
- **`decisions/`** - ADRs and technical decisions
- **`patterns/`** - Code patterns and conventions  
- **`architecture/`** - System structure and components
- **`troubleshooting/`** - Known issues and solutions

### Specialized Agents
- **`code-searcher`** - Deep codebase analysis and pattern detection
- **`memory-bank-synchronizer`** - Keeps docs aligned with code reality
- **`context-query-agent`** - Just-in-time context retrieval
- **`ux-design-expert`** - UI/UX guidance and design systems

### Context Commands
- **`/context-query "<pattern>"`** - Get focused context for a feature/path
- **`/update-memory-bank`** - Sync memory bank with current code
- **`/context-diff --since=main`** - See what changed since last update
- **`/cleanup-context`** - Archive current state for milestones

## Common Usage Patterns

### Daily Development
```bash
# Get context for the area you're working on
/context-query "src/auth/**" --limit=15

# After making changes, update the memory bank
/update-memory-bank
```

### Feature Development
```bash
# Start: Get relevant context
/context-query "Feature: Checkout" --include=decisions,patterns

# During: Update memory as you learn
/update-memory-bank --paths="src/checkout/**"

# End: Archive the completed work
/cleanup-context
```

### Code Review Prep
```bash
# Generate context for reviewers
/context-query "src/payments/**" --format=bundle --limit=20
```

## Repository Structure

**Tracked in Git**:
- `agents/` - AI agent definitions
- `commands/context/` - Context management commands  
- `workflows/memory-bank/` - Development workflow guides
- `CLAUDE.md` - Global project instructions
- `settings.json` - Claude Code configuration

**Local Only** (gitignored):
- `.claude/memory_bank/` - Your project's memory bank

## Key Workflows

### New Feature Development
1. `/context-query "Feature: YourFeature"` - Get existing context
2. Code your feature
3. `/update-memory-bank` - Document new patterns/decisions
4. `/cleanup-context` - Archive when complete

### Bug Investigation  
1. `/context-query "troubleshooting" --limit=10` - Check known issues
2. Use `code-searcher` agent for deep analysis
3. `/update-memory-bank` - Document the fix

### Project Hygiene
1. `/context-diff --since=origin/main` - See what's changed
2. `/stale-check` - Find outdated documentation
3. `/update-memory-bank` - Sync everything

## Configuration

The `settings.json` includes:
- **Permissions**: Write/edit permissions with memory bank protections

## Benefits

- **Persistent Knowledge**: Context survives between sessions
- **Focused Retrieval**: Get just the context you need, when you need it
- **Living Documentation**: Docs that stay current with code changes
- **Team Alignment**: Shared patterns and decisions accessible to all
- **Efficient Workflows**: Proven processes for common development tasks

## Pro Tips

- Use specific path patterns in queries: `src/components/**` not just `components`
- Update memory bank frequently during active development  
- Archive completed features to maintain clean active context
- Leverage specialized agents for complex tasks rather than generic prompts

This system transforms Claude from a stateless assistant into a persistent development partner that grows smarter about your project over time.