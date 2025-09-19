# CLAUDE.md

## Project Overview

* This `CLAUDE.md` defines global guidance for Claude Code.  
* It standardizes how to manage and query the context memory-bank, ensuring lean tokens and safe archive use.    

## AI Guidance

* Ignore GEMINI.md and GEMINI-*.md files  
- Keep tokens lean: use granular memory, scoped queries, minimal loads.  
- Context order: memory bank → code-searcher → archive (read-only, last resort).  
- Run tools in parallel when possible.  
- NEVER create files unless explicitly asked; ALWAYS edit existing ones.  
- Do not create docs/READMEs unless explicitly requested.  
- Do what’s requested—nothing more, nothing less.  
- Verify results before finishing.  
- Exclude `.claude/memory_bank` from commits.  

## Memory Bank System

This project uses a structured memory bank system with specialized context files. Always check these files for relevant information before starting work.

### Granular Files (authoritative for day-to-day work)

* `.claude/memory_bank/decisions/**` — ADRs and trade-offs for specific modules/features  
* `.claude/memory_bank/patterns/**` — Cross-cutting patterns and conventions  
* `.claude/memory_bank/architecture/**` — Module/service/component topology and notes  
* `.claude/memory_bank/troubleshooting/**` — Common issues and proven solutions  
* `.claude/memory_bank/stash/**` — Temporary scratch pad (only read when referenced)  

> Use `/context-query` with path scopes (and small `--limit`) to pull only the sections relevant to your task.

### Memory Bank System Backups

When asked to back up Memory Bank System files, copy the **granular memory bank directories** (`.claude/memory_bank/{decisions,patterns,architecture,troubleshooting}/`) and the `@.claude` settings directory to `@.claude/memory_backup`.  
If files already exist in the backup directory, overwrite them.  
*Exclude* `.claude/memory_bank/archive/**` from backups unless the User explicitly requests historical snapshots.

### Archive Directory

Historic documentation is stored under **`.claude/memory_bank/archive/`** to optimize memory bank size while preserving implementation history.  
Each snapshot is dated and includes `claude-architecture-complete.md` plus a `CHANGELOG.md`.  
Treat the archive as **read-only** and do not load it for routine tasks; prefer granular files. See `archive/README.md` (if present) within the archive folder for an index.

## File & Directory Tasks (FAST)

### Listing

- **All files**: `fd . -t f` (fastest, recursive)  
- **All dirs**: `fd . -t d`  
- **Respect .gitignore**: `rg --files`  
- **Current dir only**: `ls -la`  

### Search Content

- `rg "pattern"` → search all files  
- Add filters:  
  - `-i` (ignore case)  
  - `-t py` (by type)  
  - `-g "*.md"` (by glob)  
  - `-n` (line numbers)  
  - `-A 3 -B 3 "error"` (context)  
- Batch: `rg "(foo|bar|baz)"`  

### Find by Name

- `fd "name"` → fastest filename search  
- `rg --files | rg "name"` → respects `.gitignore`  

### Rules

- 🚫 Do **NOT** use: `tree`, `find`, `grep`, `ls -R`, `cat | grep`  
- ✅ Use: `fd` (names), `rg` (content), `ls -la` (local)  

### Strategy

1. Start broad → narrow (pipe rg).  
2. Filter early (`-t`, `-g`).  
3. Use batch patterns.  
4. Limit scope by path (`rg "foo" src/`).  