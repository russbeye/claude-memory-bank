# Command Template (for AI Agent)

## 0) Metadata
- **Command Title:** Cleanup Context (Archive Snapshot, Granular-First)
- **Command Type:** cleanup
- **Target Domain:** Memory Bank (documentation/context optimization)
- **Complexity Level:** medium
- **Expected Duration:** single-run (immediate execution)
- **Dependencies:** read/write access to `.claude/memory_bank/**`, ability to create dated subfolders, git (optional for reports), timestamp utility

---

## 1) Command Summary
**Objective:** Create a **read-only, dated archive snapshot** that consolidates overlapping documentation into a single comprehensive file **without deleting or modifying granular source files** (`decisions/`, `patterns/`, `architecture/`, `troubleshooting/`). Preserve modular context loading for day-to-day work while enabling historical lookup via snapshots.  
**Success Metrics:** Zero deletions of granular files; successful creation of snapshot and changelog; CLAUDE.md remains pointed at granular files with optional archive reference.  
**Scope:** Includes scanning for duplication/overlap, generating a consolidated snapshot, writing a dated `CHANGELOG.md`, and (optionally) adding a lightweight archive link to CLAUDE.md. Excludes any deletion or modification of granular files.  
**Business Impact:** Maintains **laser-focused** context windows for development (small, selective files) while preserving full historical knowledge in archives; sustained token efficiency over time.

---

## 2) Analysis Phase
### 2.1 Initial Assessment

```bash
# Size and inventory (exclude existing archive snapshots)
find .claude/memory_bank -not -path "*/archive/*" -type f -name "*.md" -exec wc -c {} \; | sort -nr
tree -a .claude/memory_bank | sed -n '1,200p'

# Optional: quick duplication/overlap signals
grep -Rin "Executive Summary\|Status:\|Coverage:" .claude/memory_bank | sed -n '1,200p'
```

**Examine for:**

Active-bank optimization opportunities (no deletions)
- Large overlapping topic areas across granular files
- Repeated executive summaries and boilerplate
- Verbose sections that belong in a historical snapshot (not daily context)
- Missing archive reference in CLAUDE.md

### 2.2 Identify Consolidation Opportunities

**High-Impact Targets (prioritize first):**

Snapshot-worthy overlaps
- Multiple files describing the same architecture domain with repetition
- Cross-cutting patterns repeated across modules
- Decision records reiterated in multiple places

**Medium-Impact Targets:**

Boilerplate & meta
- Repeated “how-to” blocks and headings shared across files
- Long historical rationale blocks better suited to archive

**Low-Impact Targets:**

Light cleanup in snapshot
- Minor duplicates like repeated link lists or footers
- Consolidated “See also/Related” sections

---

## 3) Strategy & Approach

### Phase 1: Prepare Archive Folder (High)

**Target:** Safe, dated destination that never affects the active granular bank.

**Actions:**

1. Determine run date (`YYYY-MM-DD`) and create `.claude/memory_bank/archive/YYYY-MM-DD/`
2. Verify write permissions and ensure the folder is new
3. Collect file list from active granular paths (exclude `/archive/`)

**Expected Results:** Stable destination ensures no changes to granular sources.

### Phase 2: Generate Consolidated Snapshot (High)

**Target:** Produce `claude-architecture-complete.md` in the archive with normalized, de-duplicated content.

**Common Consolidation Opportunities:**

Archive-only consolidation
- **Architecture:** unify repeated module/service descriptions
- **Patterns:** centralize cross-cutting guidelines
- **Decisions:** fold historical rationale and trade-offs into cohesive sections
- **Cross-cutting:** one place for repeated glossaries/links
- **Troubleshooting:** consolidate incident runbooks and known errors into ops runbook

**Actions:**

1. Merge overlapping sections into a single outline (Architecture → Patterns → Decisions → Cross-cutting → Troubleshooting)
2. Normalize headings, collapse near-identical passages, and remove boilerplate duplicates
3. Preserve essential technical details, examples, troubleshooting, and timelines
4. Write the result to `archive/YYYY-MM-DD/claude-architecture-complete.md`

**Expected Results:** Comprehensive, readable snapshot that removes duplication from the historical view while preserving all essentials.

### Phase 3: Changelog & Optional CLAUDE.md Reference (Medium)

**Target:** Traceability without altering the active bank’s structure or links.

**Actions:**

1. Emit `archive/YYYY-MM-DD/CHANGELOG.md` listing:
   - Source files/sections included
   - Normalizations/merges performed
   - Known gaps or “see also” anchors
2. Optionally add a single-line **archive reference** in `CLAUDE.md` (do not retarget granular links)

**Expected Results:** Clear provenance for later audits; day-to-day context remains granular-first.

### Phase 4: Validation & Safety Guarantees (Medium)

**Target:** Assure no granular deletions/edits occurred; archive is read-only afterward.

**Actions:**

1. Diff working tree to confirm no changes under `decisions/`, `patterns/`, `architecture/`, `troubleshooting/`
2. Lock snapshot semantics: do not modify `archive/YYYY-MM-DD/**` after creation
3. Record run summary (created files, counts, sizes)

**Expected Results:** Verified safety and reproducibility.

---

## 4) Implementation Guidelines

### Archive Snapshot Pattern

**Consolidated Snapshot Pattern:**

```markdown
# claude-architecture-complete.md

**Last Updated**: <YYYY-MM-DD>
**Status**: ✅ ARCHIVE SNAPSHOT (read-only)
**Coverage**: Architecture, Patterns, Decisions, Cross-cutting, Troubleshooting Rationale

## Executive Summary
<High-level overview of codebase architecture, major patterns, and key decisions>

## Architecture
<Consolidated components/modules/services with diagrams/links>

## Patterns
<Shared guidelines and styles; centralized and de-duplicated>

## Decisions (Historical)
<ADRs/rationale, trade-offs, timelines; merged from granular sources>

## Cross-cutting Concerns
<Security, performance, testing, troubleshooting; link back to granular where needed>

## Troubleshooting
<Known errors, diagnostic steps, and validated fixes; centralized and de-duplicated>
```

**Quality Standards:**

Archive-first guarantees
- Preserve all essential technical details and examples
- Maintain troubleshooting and configuration notes
- Normalize headings and remove boilerplate duplication
- Keep anchors/links resolvable (link back to granular where appropriate)
- Keep snapshot readable; avoid excessive verbosity

### Naming Convention & Location

Archive naming
- `/.claude/memory_bank/archive/YYYY-MM-DD/claude-architecture-complete.md`
- `/.claude/memory_bank/archive/YYYY-MM-DD/CHANGELOG.md`
- Never overwrite previous dated snapshots

---

## 5) Execution Process

### 1. Plan and Validate

```bash
DATE="$(date +%F)"
ARCHIVE_DIR=".claude/memory_bank/archive/${DATE}"
mkdir -p "${ARCHIVE_DIR}"
test -d "${ARCHIVE_DIR}" && echo "Archive folder ready: ${ARCHIVE_DIR}"
```

### 2. Execute by Priority

Snapshot operations
- Create archive folder
- Consolidate and write `claude-architecture-complete.md`
- Generate `CHANGELOG.md`
- (Optional) Add single-line archive reference in `CLAUDE.md`

### 3. Update References

Minimal reference updates
- Keep `CLAUDE.md` pointed at **granular** files
- Add **one** archive reference note (no link retargeting)
- Do not edit granular documents

### 4. Validate Results

```bash
# Ensure no granular files were changed or removed
git status --porcelain | grep -E "memory_bank/(decisions|patterns|architecture|troubleshooting)/" || echo "Granular bank unchanged."

# Confirm snapshot artifacts
ls -lah "${ARCHIVE_DIR}"
wc -c "${ARCHIVE_DIR}/claude-architecture-complete.md" "${ARCHIVE_DIR}/CHANGELOG.md"
```

---

## 6) Expected Outcomes

### Typical Cleanup Results

Archive snapshot benefits
- **Active-bank token usage** reduced via granular-first daily context
- **Historical completeness** preserved in archive snapshot
- **Organization** improved with de-duplicated consolidated view
- **Maintainability** improved by separating day-to-day vs. historical views
- **15–25% total token reduction** achievable as per prior optimization guidance (post-cleanup baselines).

### Success Metrics

Evidence of success
- Dated archive folder exists with snapshot + changelog
- No deletions/edits under granular paths
- CLAUDE.md continues to reference granular files
- Subsequent `/update-memory-bank` runs leave archive untouched

---

## 7) Quality Assurance

### Information Preservation Checklist

Archive integrity
- [ ] Technical details and examples preserved
- [ ] Configuration/troubleshooting retained
- [ ] Decision timelines and trade-offs captured
- [ ] Cross-references and dependencies recorded
- [ ] Links back to granular sources included

### Organization Improvement Checklist

Granular-first discipline
- [ ] Related info grouped logically in snapshot
- [ ] Clear naming and dated archive folder
- [ ] CLAUDE.md updated with archive reference (optional)
- [ ] Snapshot marked read-only/immutable
- [ ] Discoverability maintained via changelog

---

## 8) Post-Implementation Maintenance

### Regular Archive Schedule

Cadence
- **After major features**: create a new snapshot
- **Quarterly**: review snapshot readability and coverage
- **Semi-annually**: confirm archive discoverability (index or links)
- **As-needed**: when duplication creeps into active granular files

### Warning Signs for Re-execution

When to snapshot again
- Active bank begins to balloon in size
- Multiple files cover the same topic area with repetition
- Reviews note context windows get noisy
- Contributors rely on historical detail in daily work

---

## 9) Documentation Standards

### Snapshot Output File Format

```markdown
# claude-architecture-complete.md

**Last Updated**: <YYYY-MM-DD>
**Status**: ✅ ARCHIVE SNAPSHOT (read-only)
**Coverage**: Architecture, Patterns, Decisions, Cross-cutting, Troubleshooting

## Executive Summary
<Overview>

## Architecture
<Consolidated sections>

## Patterns
<Consolidated sections>

## Decisions (Historical)
<Consolidated sections>

## Cross-cutting Concerns
<Consolidated sections and references>

## Troubleshooting
<Consolidated known errors and references>
```

### Secondary Output Type Format

```markdown
# CHANGELOG.md

## Archive: <YYYY-MM-DD>
### Sources
- **<path/to/file.md>** - <summary of content merged>
- **<path/to/another.md>** - <summary of content merged>

## Notes
Reference this snapshot for historical details. Day-to-day context remains in the granular memory bank.
```
