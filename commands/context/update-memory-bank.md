# Command Template (for AI Agent)

## 0) Metadata
- **Command Title:** Update Memory Bank (Granular-First, Archive-Safe)
- **Command Type:** sync
- **Target Domain:** Memory Bank (documentation/context refresh)
- **Complexity Level:** medium
- **Expected Duration:** single-run (fast, delta-friendly)
- **Dependencies:** read/write access to `.claude/memory_bank/**`, repository read access, optional `git` for diffs/timestamps

---

## 1) Command Summary
**Objective:** Refresh the **granular memory bank** from the current repository state by updating or creating files under `decisions/`, `patterns/`, `architecture/`, and `troubleshooting/`. **Never** modify anything in `archive/**`. If granular files are missing, (re)create minimal, high-signal pages to keep context **laser-focused** for day-to-day development.  
**Success Metrics:** Granular docs reflect latest code changes; missing pages are (re)created; archive remains untouched; CLAUDE.md points to granular files; minimal token footprint via selective (delta) updates.  
**Scope:** Detect changes (optionally via git diff), parse codebase to identify decisions/patterns/architecture/troubleshooting, update existing granular files, generate missing ones, repair cross-links, and refresh references in CLAUDE.md (without retargeting to archive).  
**Business Impact:** Maintains fast, selective context windows; reduces prompt size; improves model accuracy by aligning living docs with the code.

---

## 2) Analysis Phase
### 2.1 Initial Assessment
```bash
# Optional delta base (best-effort; falls back to full scan)
git rev-parse --short HEAD 2>/dev/null || true
git diff --name-status --no-renames <BASE>..HEAD 2>/dev/null || true

# Inventory of granular docs (exclude archives)
find .claude/memory_bank -not -path "*/archive/*" -type f -name "*.md" | sort | sed -n '1,200p'
```

**Examine for:**
- Areas of the repo changed since last update (modules, services, packages).
- Existing granular pages that correspond to changed areas.
- Missing pages that should exist for new modules/decisions/patterns/troubleshooting.
- Out-of-date links or references inside granular docs.

### 2.2 Mapping Repo → Memory Bank
- **Decisions (ADRs/rationale):** new trade-offs, versioning, interfaces, deprecations.
- **Patterns (cross-cutting):** validation, caching, error handling, auth, logging.
- **Architecture (topology):** modules/services/components, boundaries, data flow.
- **Troubleshooting (bug fixing):** common issues, proven solutions, problems, error reasoning.

---

## 3) Strategy & Approach
### Phase 1: Determine Update Scope (High)
**Target:** Prefer **delta** updates when possible; fall back to scoped full scan.
- Use git diff (if available) to identify changed directories.
- Map changed paths to affected granular pages.

### Phase 2: Update or Create Granular Files (High)
**Target:** Keep files concise; extend sections idempotently.
- **Update** existing pages only in relevant sections; avoid wholesale rewrites.
- **Create** missing pages with minimal, high-signal skeletons.
- Keep links local (granular ↔ granular); do **not** point to archive except “Further reading” when necessary.

### Phase 3: Cross-Links & CLAUDE.md (Medium)
**Target:** Improve discoverability without enlarging context.
- Fix related-links between granular docs.
- Ensure `CLAUDE.md` enumerates the granular sections; **do not** retarget to archive.

### Phase 4: Validation (Medium)
**Target:** Confirm archive integrity and granular freshness.
- Verify no writes under `archive/**`.
- Ensure created/updated files pass basic structure checks (headings, sections, links).

---

## 4) Implementation Guidelines
### 4.1 Idempotent Merge Policy
- Merge by **section** (append/replace specific headings) rather than replacing entire files.
- Keep diffs minimal; preserve author notes and provenance.
- Prefer **summaries + pointers** over long narrative blocks.

### 4.2 File Naming & Placement
- `decisions/` → `kebab-case` of decision title, e.g., `api-versioning.md`
- `patterns/`  → `kebab-case` of pattern name, e.g., `request-validation.md`
- `architecture/` → `kebab-case` of module/service, e.g., `payments-service.md`
- `troubleshooting/` → `kebab-case` of module/service, e.g., `login-form.md`

### 4.3 Never Touch Archives
- `archive/**` is read-only; do not add, modify, or delete any files there.

---

## 5) Execution Process
### 1. Plan (Delta Preferred)
```bash
# If a baseline exists, compute a targeted path list; else fall back to known domain roots.
CHANGED=$(git diff --name-only <BASE>..HEAD 2>/dev/null | tr '\n' ' ')
echo "Changed paths:" $CHANGED
```

### 2. Update Granular Files
- Parse changed code and documentation.
- For each affected topic:
  - **decisions/**: update/create with problem, options, decision, consequences.
  - **patterns/**: update/create with intent, forces, examples, anti-patterns.
  - **architecture/**: update/create with responsibility, dependencies, data flow.
  - **troubleshooting/**: update/create with problem, insight, options, proven solution

### 3. Refresh References
- Ensure `CLAUDE.md` links to granular sections (index/table of contents).
- Add/repair “Related” links across granular files.

### 4. Validate
```bash
# Ensure no archive changes
git status --porcelain | grep "memory_bank/archive/" && echo "ERROR: Archive touched!" || echo "Archive untouched."

# Quick structural checks (headings exist)
grep -R "^# " .claude/memory_bank/{decisions,patterns,architecture,troubleshooting}/*.md | head -n 20
```

---

## 6) Expected Outcomes
### Typical Results
- New or updated **granular** pages accurately reflect the repo.
- Smaller, focused docs enable selective context loading.
- Better cross-linking yields faster retrieval and higher model accuracy.

### Success Metrics
- 100% of changed modules have corresponding granular updates.
- No writes under `archive/**`.
- CLAUDE.md references remain granular; context tokens decrease or stay flat.

---

## 7) Quality Assurance
### Content Checklist
- [ ] Each updated/created file contains the required sections for its type.
- [ ] Links resolve locally; “Further reading” optionally references archive.
- [ ] No duplicated boilerplate; summaries remain concise.
- [ ] Decisions list consequences; patterns include examples/anti-patterns.

### Safety Checklist
- [ ] No archive modifications.
- [ ] Minimal diffs; author intent preserved.
- [ ] Headings and structure conform to house style.

---

## 8) Post-Implementation Maintenance
### Cadence
- Run after merges or before PRs that introduce architectural change.
- Combine with delta-oriented commands for token efficiency.

### Rotations
- If many changes accumulated, follow with a quick `/stale-check` (report-only) and fix flagged items **in granular files**.