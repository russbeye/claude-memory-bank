# Rebuild Granular from Archive Command (for AI Agent)

## 0) Metadata
- **Command Title:** Rebuild Granular from Archive - Memory Bank Restoration
- **Command Type:** reconstruction/restoration
- **Target Domain:** memory bank files, archive snapshots, granular file structure
- **Complexity Level:** medium-high
- **Expected Duration:** 5-20 minutes depending on archive size and reconstruction scope
- **Dependencies:** archive directory structure, claude-architecture-complete.md files, file system write permissions

---

## 1) Command Summary
**Objective:** After a prior consolidation, (re)hydrate the **granular memory bank** from the most recent **archive snapshot** without overwriting existing granular files. Missing `decisions/*.md`, `patterns/*.md`, `architecture/*.md`, `troubleshooting/*.md` are recreated with concise, high-signal content sourced from the latest archive.  
**Success Metrics:** Missing granular files recreated with clear actionable summaries, existing files untouched, archive integrity maintained, complete audit trail provided  
**Scope:** Read-only archive analysis, missing file detection, selective granular file creation, reconstruction reporting  
**Business Impact:** Restores memory bank structure after consolidation, enables continued granular development workflow, preserves historical context  

---

## 2) Analysis Phase
### 2.1 Initial Assessment

```bash
# Check archive structure and available snapshots
ls -la .claude/memory_bank/archive/
find .claude/memory_bank/archive -name "claude-architecture-complete.md" | head -10

# Examine current granular file status
find .claude/memory_bank/decisions -name "*.md" | wc -l
find .claude/memory_bank/patterns -name "*.md" | wc -l
find .claude/memory_bank/architecture -name "*.md" | wc -l
find .claude/memory_bank/troubleshooting -name "*.md" | wc -l

# Review latest archive snapshot
ls -la .claude/memory_bank/archive/$(ls .claude/memory_bank/archive | sort | tail -1)/
```

**Examine for:**

- Available archive snapshots with complete architecture files
- Current granular file coverage vs expected structure
- Missing granular files that need reconstruction
- Archive file structure and section organization patterns

### 2.2 Identify Reconstruction Opportunities

**High-Impact Targets (prioritize first):**

- Core architecture decisions missing from granular structure
- Critical patterns and guidelines not present in granular files
- Essential component and service architecture documentation
- High-frequency reference materials needed for daily development

**Medium-Impact Targets:**

- Supporting architectural decisions that provide context
- Implementation patterns that complement existing granular files
- Historical decisions that explain current system state
- Cross-referenced sections that improve navigation
- Troubleshooting entries that document known errors, and validated fixes

**Low-Impact Targets:**

- Detailed implementation notes better suited for archive reference
- Deprecated or obsolete patterns not relevant to current development
- Overly specific documentation that exceeds granular file scope
- Content that duplicates existing granular files

---

## 3) Strategy & Approach

### Phase 1: Select Source Snapshot (High Impact)

**Target:** Identify and validate the most appropriate archive source

**Actions:**

1. Determine archive folder using `--archive-date` parameter or latest available
2. Locate `claude-architecture-complete.md` file in selected archive
3. Validate file accessibility and content structure
4. Abort with actionable error if source file missing or corrupted

**Expected Results:** Valid archive snapshot with complete architecture documentation

### Phase 2: Parse Snapshot Structure (High Impact)

**Target:** Build comprehensive section index from archive content

**Common Section Categorization Patterns:**

- **Decisions Category:** Headings matching `(Decision|ADR|Rationale|Trade-offs)` patterns
- **Patterns Category:** `(Pattern|Style|Template|Guideline)` for coding and implementation standards
- **Architecture Category:** `(Architecture|Module|Service|Component|Topology|Diagram)` for system structure
- **Cross-Link Resolution:** Capture "Related" and "See also" references for comprehensive context
- **Troubleshooting Category:** `(Troubleshooting|Known Error|Runbook|Incident|Diagnostic|Resolution|KEDB)` for known issues, diagnostic steps, and validated fixes

**Actions:**

1. Parse archive file using H2/H3 heading structure for section identification
2. Apply categorization rules to classify content by granular file type
3. Build section index with heading anchors and content boundaries
4. Resolve cross-references and related section mappings

**Expected Results:** Complete section index with category assignments and cross-references

### Phase 3: Plan Reconstruction (High Impact)

**Target:** Generate detailed reconstruction plan for missing granular files

**Actions:**

1. Enumerate missing granular files under decisions, patterns, architecture, troubleshooting directories
2. Apply `--paths` filter and `--limit-sections` parameter to scope reconstruction
3. Map archive sections to target granular file paths with provenance tracking
4. Build reconstruction plan including file naming and content generation strategy

**Expected Results:** Comprehensive reconstruction plan with file mappings and metadata

### Phase 4: Seed Files with Granular-First Strategy (High Impact)

**Target:** Create missing granular files without overwriting existing content

**Content Generation Strategy Options:**

- **Extract Mode:** Copy section body with light cleanup, strip cross-archive anchors, fix local links
- **Summarize Mode:** Concise distillation with bullets, decisions, implications, and links (default)
- **Minify Headings:** Normalize and shorten long headings for better file organization
- **Character Limits:** Respect `--max-chars-per-file` with truncation and archive references

**Actions:**

1. Verify no existing granular files will be overwritten
2. Create new files with proper front matter and provenance comments
3. Apply selected content generation strategy (extract or summarize)
4. Generate canonical filenames from headings with proper slug conversion
5. Fix internal links to point to local granular counterparts when available

**Expected Results:** New granular files with high-quality content and proper metadata

### Phase 5: Emit Reports (Medium Impact)

**Target:** Generate comprehensive reconstruction audit trail

**Actions:**

1. Create timestamped report in `.claude/memory_bank/reports/` directory
2. Include source archive path, date, and processing parameters
3. List all created files with source section references
4. Document skipped files (already existed) and any applied truncations
5. Honor `--dry-run` and `--report-only` flags by skipping file creation

**Expected Results:** Complete audit trail with reconstruction details and validation data

---

## 4) Implementation Guidelines

### Archive Parsing Patterns

**Section Classification Logic:**

```bash
# Decision pattern matching
if grep -i -E "(decision|adr|rationale|trade-off)" <<< "$heading"; then
  category="decisions"
fi

# Pattern recognition
if grep -i -E "(pattern|style|template|guideline)" <<< "$heading"; then
  category="patterns"
fi

# Architecture identification  
if grep -i -E "(architecture|module|service|component|topology|diagram)" <<< "$heading"; then
  category="architecture"
fi

# Troubleshooting classification
if grep -i -E "(troubleshooting|known[[:space:]-]?error(s)?|runbook(s)?|incident(s)?|diagnostic(s)?|resolution(s)?|kedb)" <<< "$heading"; then
  category="troubleshooting"
fi
```

**Quality Standards:**

- Parse archive content without data loss or corruption
- Maintain heading hierarchy and section boundaries accurately
- Preserve cross-references and internal link structure
- Generate consistent, canonical filenames from headings
- Include complete provenance information in all created files

### File Seeding Conventions

**Filename Generation Pattern:**
- `Decision: API Versioning → decisions/api-versioning.md`
- `Pattern: Request Validation → patterns/request-validation.md`  
- `Architecture: Payments Service → architecture/payments-service.md`
- `Troubleshooting: Payment Timeout → troubleshooting/payment-timeout.md`

**Provenance Footer Template:**
```markdown
---
**Provenance**: .claude/memory_bank/archive/<date>/claude-architecture-complete.md#<anchor>
**Seed Strategy**: <extract|summarize>
**Seeded On**: <ISO timestamp>
**Max Chars**: <limit applied>
```

---

## 5) Execution Process

### 1. Plan and Validate

```bash
# Verify archive structure
test -d .claude/memory_bank/archive/

# Check for latest archive or validate specific date
ARCHIVE_DATE=${archive_date:-$(ls .claude/memory_bank/archive | sort | tail -1)}
test -f .claude/memory_bank/archive/$ARCHIVE_DATE/claude-architecture-complete.md

# Validate granular directories exist
mkdir -p .claude/memory_bank/decisions/ .claude/memory_bank/patterns/ .claude/memory_bank/architecture/ .claude/memory_bank/troubleshooting/
```

### 2. Execute by Priority

- **Parameter Processing:** Parse all CLI flags including archive-date, strategy, paths, limits, dry-run options
- **Archive Analysis:** Select source snapshot and parse section structure with categorization
- **Reconstruction Planning:** Build file mapping with missing file detection and scope filtering  
- **Granular File Creation:** Generate new files using specified strategy while preserving existing content
- **Report Generation:** Create comprehensive audit trail with reconstruction details

### 3. Update References

- Write detailed reconstruction report to timestamped file in reports directory
- Include complete provenance information in all created granular files
- Update cross-references between newly created granular files
- Maintain archive read-only status throughout process

### 4. Validate Results

```bash
# Verify reconstruction report creation
test -f .claude/memory_bank/reports/rebuild-from-archive-$(date +%Y-%m-%d).md

# Check created files have proper structure
find .claude/memory_bank/{decisions,patterns,architecture,troubleshooting} -name "*.md" -newer /tmp/rebuild_start | xargs head -5

# Validate no existing files were modified
git status .claude/memory_bank/{decisions,patterns,architecture,troubleshooting}
```

---

## 6) Expected Outcomes

### Typical Archive Reconstruction Results

- **Selective File Creation** - Only missing granular files created without overwriting existing content
- **Content Quality** - High-signal summaries or extracts optimized for development workflow
- **Archive Integrity** - Source archive remains completely untouched and read-only
- **Complete Provenance** - Every created file includes full source attribution and generation metadata
- **Workflow Continuity** - Restored granular structure supports continued memory bank development

### Success Metrics

- Number of missing granular files successfully recreated
- Archive sections processed and categorized correctly
- Content generation strategy effectiveness (character limits, truncations applied)
- Cross-reference resolution success rate and link fixing

---

## 7) Quality Assurance

### Content Integrity Checklist

- [ ] Archive content parsed accurately without data loss or corruption
- [ ] All created files contain high-quality, actionable content within size limits
- [ ] No existing granular files modified or overwritten during reconstruction
- [ ] Cross-references and internal links properly resolved and updated
- [ ] Provenance information complete and accurate for all generated files

### Process Compliance Checklist

- [ ] Archive directory maintained as completely read-only throughout process
- [ ] CLI parameters (--paths, --limit-sections, --max-chars-per-file) honored correctly
- [ ] Dry-run and report-only modes function without creating actual files
- [ ] File naming conventions followed consistently with proper slug generation
- [ ] Reconstruction report includes complete audit trail with all processing details

---

## 8) Post-Implementation Maintenance

### Regular Archive Reconstruction Schedule

- **After Consolidation**: Run immediately after memory bank consolidation to restore granular structure
- **Monthly**: Review reconstruction reports to optimize section categorization and content strategies
- **Quarterly**: Analyze archive snapshot quality and update parsing logic for improved categorization
- **As-needed**: Rebuild specific categories when granular files are accidentally deleted or corrupted

### Warning Signs for Reconstruction Issues

- Frequent reconstruction failures due to missing or corrupted archive snapshots
- Generated granular files consistently exceeding optimal size limits
- Poor section categorization resulting in files in wrong granular directories
- Cross-reference resolution failing due to archive structure changes

---

## 9) Documentation Standards

### Reconstruction Report Format

```markdown
# Archive Reconstruction Report - YYYY-MM-DD

**Source Archive**: .claude/memory_bank/archive/YYYY-MM-DD/claude-architecture-complete.md
**Generated**: [ISO timestamp]
**Parameters**: --strategy=[extract/summarize] --paths=[filters] --limit-sections=[n]
**Total Sections**: [count processed]

---

## Reconstruction Summary
- **Strategy Used**: [extract/summarize]
- **Archive Date**: [YYYY-MM-DD]
- **Sections Categorized**: [decisions/patterns/architecture/troubleshooting counts]
- **Character Limits Applied**: [max per file]

---

## Created Files
### Decisions
- **decisions/api-versioning.md** - Decision: API Versioning Strategy
  - **Source**: Section "API Versioning Decision" (lines 245-289)
  - **Strategy**: summarize
  - **Characters**: 2,847 / 8,000

### Patterns  
- **patterns/request-validation.md** - Pattern: Request Validation
  - **Source**: Section "Request Validation Guidelines" (lines 456-523)
  - **Strategy**: extract
  - **Characters**: 6,234 / 8,000

### Architecture
- **architecture/payments-service.md** - Architecture: Payments Service
  - **Source**: Section "Payments Service Design" (lines 789-856)
  - **Strategy**: summarize
  - **Characters**: 7,891 / 8,000 (truncated)

### Troubleshooting
- **troubleshooting/payment-timeout.md** - Troubleshooting: Payment Timeout
  - **Source**: Section "Payment Timeout – Runbook & KEDB" (lines 1421–1498)
  - **Strategy**: summarize
  - **Characters**: 7,945 / 8,000 (truncated)
---

## Skipped Files (Already Existed)
- decisions/database-migration.md
- patterns/error-handling.md

---

## Processing Notes
- Applied --minify-headings normalization to 3 files
- Truncated 1 file due to character limit with archive reference
- Resolved 12 cross-references to local granular files
```

### Granular File Provenance Format

```markdown
<!-- Seeded from archive 2024-01-15, section: "API Versioning Decision". Verify & refine. -->

# API Versioning Strategy

[Generated content based on archive section]

---

**Provenance**: .claude/memory_bank/archive/2024-01-15/claude-architecture-complete.md#api-versioning-decision
**Seed Strategy**: summarize
**Seeded On**: 2024-01-15T10:30:00Z
**Max Chars**: 8000
```

---

## Agent Prompting Tips (how to use this command)

### Command Usage Patterns
- **Basic Restoration**: `/rebuild-granular-from-archive` - Use latest archive with default settings
- **Specific Archive**: `/rebuild-granular-from-archive --archive-date=2024-01-15` - Use particular snapshot
- **Scoped Rebuild**: `/rebuild-granular-from-archive --paths="decisions/**,patterns/**"` - Limit to specific categories
- **Planning Mode**: `/rebuild-granular-from-archive --dry-run` - Preview without creating files

### Strategy Selection Guidelines
- **--strategy=extract**: Use when archive content is already well-structured and concise
- **--strategy=summarize**: Use when archive sections are verbose and need distillation (default)
- **--minify-headings**: Apply when archive headings are overly long or inconsistent
- **--max-chars-per-file**: Adjust based on desired granular file sizes and context window limits

### File Organization Best Practices
- **Canonical Naming**: Ensure generated filenames follow consistent slug patterns
- **Category Accuracy**: Verify section categorization logic matches your memory bank organization
- **Cross-Reference Maintenance**: Check that internal links point to correct granular files
- **Provenance Tracking**: Always include complete source attribution for audit trails

### Integration with Memory Bank Workflow
- **Post-Consolidation**: Run immediately after consolidation to restore working structure
- **Selective Recovery**: Use path filters to rebuild only specific areas when needed
- **Quality Enhancement**: Follow up with `/update-memory-bank` to refine and enhance seeded content
- **Archive Preservation**: Never modify archive content - always maintain read-only access

### Troubleshooting Reconstruction Issues
- **Missing Archives**: Verify archive directory structure and claude-architecture-complete.md files exist
- **Categorization Errors**: Review section heading patterns and adjust classification logic
- **Size Limit Issues**: Tune --max-chars-per-file based on archive content density
- **Link Resolution Failures**: Check cross-reference patterns and ensure granular file structure consistency