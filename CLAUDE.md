# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working in this repository.

## AI Guidance

* Ignore GEMINI.md and GEMINI-*.md files
* To save main context space, for code searches, inspections, troubleshooting or analysis, use code-searcher subagent where appropriate - giving the subagent full context background for the task(s) you assign it.
* After receiving tool results, carefully reflect on their quality and determine optimal next steps before proceeding. Use your thinking to plan and iterate based on this new information, and then take the best next action.
* For maximum efficiency, whenever you need to perform multiple independent operations, invoke all relevant tools simultaneously rather than sequentially.
* Before you finish, please verify your solution
* Do what has been asked; nothing more, nothing less.
* NEVER create files unless they're absolutely necessary for achieving your goal.
* ALWAYS prefer editing an existing file to creating a new one.
* NEVER proactively create documentation files (*.md) or README files. Only create documentation files if explicitly requested by the User.
* When you update or modify core context files, also update markdown documentation and memory bank
* When asked to commit changes, exclude `./claude/memory_bank` files from any commits. Never delete these files.

**Context selection order (keep tokens lean):**
1. **Granular memory bank** for the relevant paths (see "Memory Bank System → Granular Files" below)
2. `code-searcher` for pinpoint lookups
3. **Archive snapshots** *only if explicitly requested* or when historical depth is required

**Command quickstart (for focused context workflows):**
* Daily delta refresh: `/context-diff --since=origin/main --paths="<your-globs>"`
* Just-in-time bundle: `/context-query "<path-or-feature>" --include=decisions,patterns --limit=8`
* Refresh granular docs: `/update-memory-bank`
* Create read-only snapshot: `/cleanup-context` (writes to `.claude/memory_bank/archive/YYYY-MM-DD/`)
* Rehydrate missing granular pages: `/rebuild-granular-from-archive --strategy=summarize`
* Hygiene: `/stale-check --paths="<your-globs>" --no-mark`

**Archive safety rules:**
* Treat everything under `.claude/memory_bank/archive/**` as **read-only**.
* **Do not** append to or rely on archived snapshots for day-to-day work; prefer granular files.
* Prefer small, scoped reads (paths + low `--limit`) to avoid loading unnecessary tokens.

## Memory Bank System

This project uses a structured memory bank system with specialized context files. Always check these files for relevant information before starting work:

### Granular Files (authoritative for day-to-day work)

* `.claude/memory_bank/decisions/**` — ADRs and trade-offs for specific modules/features
* `.claude/memory_bank/patterns/**` — Cross-cutting patterns and conventions
* `.claude/memory_bank/architecture/**` — Module/service/component topology and notes
* `.claude/memory_bank/troubleshooting/**` — Common issues and proven solutions
* `.claude/memory_bank/stash/**` — Temporary scratch pad (only read when referenced)

> Use `/context-query` with path scopes (and small `--limit`) to pull only the sections relevant to your task.

### Memory Bank System Backups

When asked to backup Memory Bank System files, you will copy the **granular memory bank directories** (`.claude/memory_bank/{decisions,patterns,architecture/troubleshooting}/`) and the `@.claude` settings directory to `@.claude/memory_backup`. If files already exist in the backup directory, you will overwrite them.  
*Exclude* `.claude/memory_bank/archive/**` from backups unless the User explicitly requests historical snapshots.

### Archive Directory

Historic documentation is stored under **`.claude/memory_bank/archive/`** to optimize memory bank size while preserving implementation history. Each snapshot is dated and includes `claude-architecture-complete.md` plus a `CHANGELOG.md`. Treat the archive as **read-only** and **do not** load it for routine tasks; prefer granular files. See `archive/README.md` (if present) within the archive folder for an index.

## Project Overview

## ALWAYS START WITH THESE COMMANDS FOR COMMON TASKS

**Task: "List/summarize all files and directories"**

```bash
fd . -t f           # Lists ALL files recursively (FASTEST)
# OR
rg --files          # Lists files (respects .gitignore)
```

**Task: "Search for content in files"**

```bash
rg "search_term"    # Search everywhere (FASTEST)
```

**Task: "Find files by name"**

```bash
fd "filename"       # Find by name pattern (FASTEST)
```

### Directory/File Exploration

```bash
# FIRST CHOICE - List all files/dirs recursively:
fd . -t f           # All files (fastest)
fd . -t d           # All directories
rg --files          # All files (respects .gitignore)

# For current directory only:
ls -la              # OK for single directory view
```

### BANNED - Never Use These Slow Tools

* ❌ `tree` - NOT INSTALLED, use `fd` instead
* ❌ `find` - use `fd` or `rg --files`
* ❌ `grep` or `grep -r` - use `rg` instead
* ❌ `ls -R` - use `rg --files` or `fd`
* ❌ `cat file | grep` - use `rg pattern file`

### Use These Faster Tools Instead

```bash
# ripgrep (rg) - content search 
rg "search_term"                # Search in all files
rg -i "case_insensitive"        # Case-insensitive
rg "pattern" -t py              # Only Python files
rg "pattern" -g "*.md"          # Only Markdown
rg -1 "pattern"                 # Filenames with matches
rg -c "pattern"                 # Count matches per file
rg -n "pattern"                 # Show line numbers 
rg -A 3 -B 3 "error"            # Context lines
rg " (TODO| FIXME | HACK)"      # Multiple patterns

# ripgrep (rg) - file listing 
rg --files                      # List files (respects •gitignore)
rg --files | rg "pattern"       # Find files by name 
rg --files -t md                # Only Markdown files 

# fd - file finding 
fd -e js                        # All •js files (fast find) 
fd -x command {}                # Exec per-file 
fd -e md -x ls -la {}           # Example with ls 

# jq - JSON processing 
jq. data.json                   # Pretty-print 
jq -r .name file.json           # Extract field 
jq '.id = 0' x.json             # Modify field
```

### Search Strategy

1. Start broad, then narrow: `rg "partial" | rg "specific"`
2. Filter by type early: `rg -t python "def function_name"`
3. Batch patterns: `rg "(pattern1|pattern2|pattern3)"`
4. Limit scope: `rg "pattern" src/`

### INSTANT DECISION TREE

```
User asks to "list/show/summarize/explore files"?
  → USE: fd . -t f  (fastest, shows all files)
  → OR: rg --files  (respects .gitignore)

User asks to "search/grep/find text content"?
  → USE: rg "pattern"  (NOT grep!)

User asks to "find file/directory by name"?
  → USE: fd "name"  (NOT find!)

User asks for "directory structure/tree"?
  → USE: fd . -t d  (directories) + fd . -t f  (files)
  → NEVER: tree (not installed!)

Need just current directory?
  → USE: ls -la  (OK for single dir)
```