# Command Template (for AI Agent)

## 0) Metadata
- **Command Title:** Update Memory Bank (Bootstrap & Delta; Granular-First, Archive-Safe)
- **Command Type:** sync
- **Target Domain:** Memory Bank (documentation/context refresh)
- **Complexity Level:** high (bootstrap) / medium (delta)
- **Expected Duration:** single-run; intensive on first run (bootstrap), fast on subsequent (delta)
- **Dependencies:** read/write access to `.claude/memory_bank/**`, repository read access, optional `git` for diffs/timestamps, optional graph tools (e.g., import graph)
- **Agent:** memory-bank-synchronizer

---

## 1) Command Summary
**Objective:** Build and maintain a comprehensive **granular memory bank** from the current repository. On first run or when explicitly requested, perform a **Bootstrap** pass that aggressively discovers **state management**, **data flow**, **integration boundaries**, **team patterns**, and **decision history** to seed rich documentation across `decisions/`, `patterns/`, `architecture/`, and `troubleshooting/`. On subsequent runs, perform a **Delta** pass that updates only what changed since the last run. **Never** modify anything in `archive/**`.

**Success Metrics:**
- **Bootstrap:** Achieve target coverage thresholds (see §3.2) with stack-aware discoveries.
- **Delta:** Update only impacted pages; maintain cross-links; preserve archive integrity.
- **Both:** CLAUDE.md references remain granular; selective context stays token-lean.

**Scope:** Detect changes (via git diff if available), infer technology stack and frameworks, map module/service topology and data flow, identify reusable patterns and team conventions, extract/validate decisions, and record troubleshooting knowledge. Generate/update granular files, establish cross-links, refresh CLAUDE.md references (without retargeting to archive).

**Business Impact:** New contributors get an accurate map of **how the system works** (not just what files exist). Day-to-day prompts stay small; onboarding and code review speed up.

---

## 2) Analysis Phase
### 2.1 Initial Assessment
```bash
# Decide Bootstrap vs Delta
if [ -z "$(find .claude/memory_bank -maxdepth 2 -type f -name '*.md' -not -path '*/archive/*' | head -n1)" ]; then
  MODE="bootstrap"
else
  MODE="${MODE:-delta}"
fi
echo "Update mode: $MODE"

# Optional delta base (best-effort; falls back to full scan)
git rev-parse --short HEAD 2>/dev/null || true
git diff --name-status --no-renames <BASE>..HEAD 2>/dev/null || true

# Inventory of granular docs (exclude archives)
find .claude/memory_bank -not -path "*/archive/*" -type f -name "*.md" | sort | sed -n '1,200p'
```

**Examine for:**
- Recently changed areas (modules, services, packages).
- Existing granular pages that correspond to changed areas.
- Missing pages for new modules, patterns, decisions, troubleshooting.
- Obvious gaps in **state management**, **data flow**, **integration** docs.

### 2.2 Technology Stack & Framework Discovery
Detect stack and frameworks to drive targeted heuristics.

```bash
detect_stack() {
  [[ -f "package.json" ]] && echo "javascript" && return
  [[ -f "go.mod" ]] && echo "go" && return
  [[ -f "requirements.txt" || -f "pyproject.toml" ]] && echo "python" && return
  echo "generic"
}

discover_frameworks() {
  case "$1" in
    javascript)
      jq -r '.dependencies,.devDependencies | keys[]?' package.json 2>/dev/null | \
        grep -E "(react|next|redux|@reduxjs/toolkit|zustand|jotai|xstate|mobx|recoil|rxjs|swr|@tanstack/query|apollo|urql|graphql|vite|jest|cypress)" | sort -u ;;
    go)
      grep -E "(gin|echo|fiber|gorilla|chi)" go.mod go.sum 2>/dev/null | awk '{print $1}' | sort -u ;;
    python)
      grep -E "(django|flask|fastapi|pydantic|pytest|celery)" requirements.txt pyproject.toml 2>/dev/null | sort -u ;;
  esac
}
```

### 2.3 Data Flow & State Management Mapping (Bootstrap emphasis)
- Build a **module import graph** (or approximate via static greps) to infer **boundaries** and **call paths**.
- Detect **state management** patterns (e.g., Redux slices, Zustand stores, XState machines, RxJS observables, React Query caches).
- Identify **integration points** (API clients, message queues, DB adapters).
- Record **typical flows** (e.g., UI → Hook → Store → API → Cache → Rerender).

### 2.4 Mapping Repo → Memory Bank
- **Decisions:** trade-offs and alternatives; versioning; deprecations; performance strategies.
- **Patterns:** reusable templates and conventions; framework-specific idioms; testing & error handling.
- **Architecture:** components/services; boundaries; interfaces; **data flow diagrams**.
- **Troubleshooting:** recurring issues; diagnostics; validated fixes; affected versions.

---

## 3) Strategy & Approach
### 3.1 Determine Update Mode
- `--mode=bootstrap` (or empty memory bank) → **comprehensive intake**.
- `--mode=delta` (default) → only changed areas.

### 3.2 Bootstrap Coverage Targets (minimums; adjust with flags)
- **Architecture:** ≥ 8 modules/services with responsibilities, interfaces, data flow.
- **Patterns:** ≥ 12 entries, including **state management** and **data fetching** patterns.
- **Decisions:** ≥ 10 ADRs (stack choices, API style, caching, deployment, testing).
- **Troubleshooting:** ≥ 6 KEDB items (symptoms, diagnostics, fix, affected versions).
- Quotas configurable: `--targets="arch=8,patterns=12,decisions=10,troubleshooting=6"`.

### 3.3 Discovery Heuristics (stack-aware)
**JavaScript/React examples:**
- **State:** search for `createSlice|configureStore|useSelector|dispatch|create|useStore|atom|useMachine|createMachine|Subject|Observable`.
- **Data fetching:** `useQuery|useMutation|swr|apollo|urql|fetch|axios.*interceptors`.
- **Routing:** `next/navigation|react-router`.
- **Error boundaries:** `componentDidCatch|ErrorBoundary`.
- **Testing:** `jest|vitest|cypress` configs and `describe|it` density.

**Go/Python examples:** DI patterns, concurrency/async, handler/router registration, config loaders, error wrappers, tests.

### 3.4 Categorization & Scoring
Score discovery candidates to prioritize what to write first:
- **frequency 25% + complexity 20% + team adoption 15% + change frequency 15% + cross-module usage 10% + business impact 10% + maintenance cost 5%**.

Auto-route to: `decisions/`, `architecture/`, `patterns/`, `troubleshooting/` (see §4.4).

### 3.5 Cross-Links & CLAUDE.md
Connect related content across categories. Keep CLAUDE.md pointing to **granular** sections only.

### 3.6 Validation
Ensure archive untouched; verify headings/links; insert `<!-- STALE: ... -->` where verification is needed.

---

## 4) Implementation Guidelines
### 4.1 Idempotent Merge Policy
- Merge **by section**; avoid wholesale rewrites. Preserve provenance.
- Prefer **summaries + code pointers** over large blobs.

### 4.2 File Naming & Placement
- `decisions/` → `api-versioning.md`
- `patterns/` → `request-validation.md`
- `architecture/` → `payments-service.md`
- `troubleshooting/` → `payment-timeout.md`

### 4.3 Never Touch Archives
- `archive/**` is read-only; never write/modify/delete there.

### 4.4 Discovery-to-Category Mapping
```javascript
const categorizeDiscovery = (d) => {
  if (d.hasAlternatives && d.hasTradeoffs) return 'decisions/';
  if (d.definesStructure || d.showsDependencies) return 'architecture/';
  if (d.isReusable && d.hasImplementation) return 'patterns/';
  if (d.hasProblem && d.hasSolution) return 'troubleshooting/';
};
```

### 4.5 Content Templates (per category)
**Decisions** — Context · Options · Decision · Consequences · Related  
**Architecture** — Responsibility · Interfaces & Dependencies · Data Flow · Operational Notes · Related  
**Patterns** — Intent · Forces · Solution · Examples · Anti-Patterns · Related  
**Troubleshooting** — Symptoms · Diagnostics · Fix · Affected Versions · Prevention · Related

---

## 5) Execution Process
### 1. Plan (Decide Mode; detect stack)
```bash
STACK=$(detect_stack)
FRAMEWORKS=$(discover_frameworks "$STACK")
echo "Detected stack: $STACK"
echo "Frameworks: $FRAMEWORKS"
```

### 2A. Bootstrap Path
- Build import/dependency graph (or approximations).
- Run stack-specific greps to harvest **state**, **data flow**, **integration**, and **testing** signals.
- Generate discovery candidates, score them, and write until **coverage targets** (see §3.2) are met per category.
- Seed missing granular files with concise, high-signal content + code pointers.

### 2B. Delta Path
- Compute `git diff` scope; map to affected pages; run the same heuristics **only** within changed areas.
- Update impacted sections; add new items when thresholds are exceeded.

### 3. Refresh References
- Maintain Related links among categories and modules. Keep CLAUDE.md granular.

### 4. Validate
```bash
# Ensure no archive changes
git status --porcelain | grep "memory_bank/archive/" && echo "ERROR: Archive touched!" || echo "Archive untouched."

# Structural checks
grep -R "^# " .claude/memory_bank/{decisions,patterns,architecture,troubleshooting}/*.md | head -n 20
```

---

## 6) Expected Outcomes
### Bootstrap
- 8–15 architecture pages with high-level **data flow**.
- 12–20 patterns, including **state management** and **data fetching**.
- 10–16 decisions with trade-offs and consequences.
- 6–12 troubleshooting entries with diagnostics and fixes.

### Delta
- Only pages related to changed code are updated/created.
- Cross-links stay accurate; archive remains read-only.

### Success Metrics
- New contributors can follow **how data and state move** through the system.
- Subsequent `/context-query` calls return small, precise bundles.

---

## 7) Quality Assurance
### Content Checklist
- [ ] Decisions have outcomes; patterns have examples/anti-patterns.
- [ ] Architecture shows boundaries and **data flow**.
- [ ] Troubleshooting includes diagnostics and affected versions.
- [ ] Links resolve locally; archive only for historical references.

### Safety Checklist
- [ ] No archive modifications.
- [ ] Minimal diffs; author intent preserved.
- [ ] Headings and structure conform to house style.

---

## 8) Post-Implementation Maintenance
### Cadence
- Run **bootstrap** on new repos or when memory bank is empty.
- Run **delta** after merges or prior to PRs with architectural impact.

### Rotations
- Pair with `/stale-check` (report-only) when large refactors land.
- Consider `/cleanup-context` to snapshot history after major milestones.

---

## 9) Documentation Standards
Provide skeletons as in §4.5 and prefer short, actionable text with links to code locations for detail.
