# Stale Context Check Command (for AI Agent)

## 0) Metadata
- **Command Title:** Stale Context Check - Memory Bank Validation and Detection
- **Command Type:** analysis/detection
- **Target Domain:** memory bank files, repository analysis, content validation
- **Complexity Level:** medium
- **Expected Duration:** 5-10 minutes depending on memory bank size and repository scope
- **Dependencies:** git, stale-context-detector, memory bank directory structure, repository access

---

## 1) Command Summary
**Objective:** Identify stale, contradictory, or obsolete guidance in the memory bank by invoking `stale-context-detector`. Marks questionable sections and produces a review report with actionable recommendations for content maintenance.  
**Success Metrics:** Clear list of suspect sections with commit references, optional inline markers guide reviewers, report is concise and actionable  
**Scope:** Memory bank content analysis, repository comparison, stale content detection, non-destructive marking, comprehensive reporting  
**Business Impact:** Maintains memory bank accuracy and relevance, prevents outdated guidance from misleading development, improves documentation quality  

---

## 2) Analysis Phase
### 2.1 Initial Assessment

```bash
# Check memory bank structure and content volume
find .claude/memory_bank -name "*.md" -not -path "*/archive/*" | wc -l
du -sh .claude/memory_bank/decisions/ .claude/memory_bank/patterns/ .claude/memory_bank/architecture/ .claude/memory_bank/troubleshooting/

# Examine repository status and recent changes
git status
git log --oneline -20

# Review existing stale context reports
ls -la .claude/memory_bank/reports/stale-context-*.md | tail -5
```

**Examine for:**

- Memory bank content volume and organizational structure
- Recent repository changes that might impact documentation relevance
- Current git commit baseline for comparison analysis
- Historical stale context detection patterns and trends

### 2.2 Identify Stale Content Opportunities

**High-Impact Targets (prioritize first):**

- References to removed or renamed code files and functions
- Patterns and decisions that conflict with current implementation
- Sections with outdated technology stack or architectural assumptions
- Documentation referencing deprecated APIs or obsolete practices

**Medium-Impact Targets:**

- Sections older than significant commits with no recent validation
- Cross-references to moved or restructured code components
- Implementation examples that no longer match current codebase patterns
- Architectural decisions that may have been superseded by recent changes
- Troubleshooting known-error entries no longer align with current code or infrastructure

**Low-Impact Targets:**

- Minor inconsistencies in naming or terminology
- Slightly outdated but still generally applicable guidance
- Documentation with cosmetic issues that don't affect core accuracy
- Historical context that remains valuable despite age

---

## 3) Strategy & Approach

### Phase 1: Scan Memory Bank and Repository (High Impact)

**Target:** Comprehensive analysis of memory bank content against current repository state

**Actions:**

1. Scan `.claude/memory_bank/**` excluding archive directory for content analysis
2. Apply `--paths` filter to limit repository scope if specified
3. Use `--since` parameter to establish git comparison baseline or use recent commits
4. Build comprehensive inventory of memory bank references to code elements

**Expected Results:** Complete mapping of memory bank content to repository elements

### Phase 2: Detect Stale and Contradictory Content (High Impact)

**Target:** Identify specific issues requiring attention or validation

**Common Stale Content Detection Patterns:**

- **Removed Code References:** Files, functions, classes that no longer exist in repository
- **Implementation Conflicts:** Documented patterns that contradict current code structure
- **Outdated Technology:** References to deprecated frameworks, tools, or approaches
- **Structural Changes:** Architecture descriptions that don't match current system organization

**Actions:**

1. Detect references to removed or renamed code elements using git history
2. Identify patterns and decisions that conflict with current implementation
3. Find sections older than N commits with no validation or updates
4. Cross-reference memory bank content with current repository structure

**Expected Results:** Categorized list of stale content with specific issue types and severity

### Phase 3: Mark Questionable Sections (Medium Impact)

**Target:** Provide non-intrusive in-place guidance for reviewers

**Actions:**

1. Insert `<!-- STALE: verify against repo commit <hash> -->` markers above suspect sections
2. Apply marking only when `--mark` flag is active (default behavior)
3. Skip marking when `--no-mark` flag is specified (report-only mode)
4. Ensure minimal diffs and readable markup that doesn't disrupt content flow

**Expected Results:** Marked memory bank files with clear indicators for manual review

### Phase 4: Generate Comprehensive Report (High Impact)

**Target:** Produce actionable report with specific guidance for content maintenance

**Actions:**

1. Create timestamped report in `.claude/memory_bank/reports/stale-context-YYYY-MM-DD.md`
2. Include file and line number hints with specific reasons for flagging
3. Provide suggested next steps for each identified issue
4. Categorize findings by severity and type for prioritized remediation
5. Include summary statistics and trends for memory bank health assessment

**Expected Results:** Detailed report enabling efficient and targeted content maintenance

---

## 4) Implementation Guidelines

### Stale Content Detection Patterns

**Code Reference Validation:**

```bash
# Check if referenced files exist
while read -r file_ref; do
  if [[ ! -f "$file_ref" ]]; then
    echo "STALE: File reference $file_ref not found"
  fi
done < <(grep -o 'src/[^ ]*\.js' memory_bank_files)

# Validate function/class references
git grep -n "function $function_name\|class $class_name" || echo "STALE: $function_name not found"

# Check git history for file moves/renames
git log --follow --name-status -- "$file_path" | head -20
```

**Quality Standards:**

- Preserve all original content without deletion or corruption
- Insert markers that are clearly distinguishable but non-disruptive
- Maintain version control friendliness with minimal diff impact
- Provide specific, actionable recommendations for each detected issue
- Include sufficient context for reviewers to make informed decisions

### Marking Convention Standards

**Inline Marker Format:**
- `<!-- STALE: verify against repo commit abc123 - [specific reason] -->`
- `<!-- STALE: function removeUser() not found in src/auth/users.js -->`
- `<!-- STALE: pattern conflicts with current implementation - see commit def456 -->`

**Report Reference Format:**
- Include file paths with line numbers for precise navigation
- Provide git commit hashes for historical context and verification
- Categorize issues by type (missing code, conflicting pattern, outdated decision)

---

## 5) Execution Process

### 1. Plan and Validate

```bash
# Verify memory bank structure and access
test -d .claude/memory_bank/
test -w .claude/memory_bank/reports/

# Check git repository status and access
git rev-parse --git-dir
git log --oneline -1

# Validate optional parameters
PATHS=${paths:-""}
SINCE=${since:-"HEAD~20"}
MARK_MODE=${mark:-true}
```

### 2. Execute by Priority

- **Parameter Processing:** Parse CLI flags including paths filter, marking mode, and git baseline
- **Content Scanning:** Analyze memory bank files and build reference inventory
- **Repository Analysis:** Compare memory bank content against current repository state
- **Issue Detection:** Identify stale, contradictory, or obsolete content with specific categorization
- **Marking Application:** Insert non-intrusive comments when marking mode is enabled
- **Report Generation:** Create comprehensive report with actionable recommendations

### 3. Update References

- Write detailed stale context report to timestamped file in reports directory
- Apply inline markers to memory bank files when --mark is enabled
- Maintain clean version control history with minimal, targeted changes
- Log detection summary with key metrics and identified issues

### 4. Validate Results

```bash
# Verify report generation
test -f .claude/memory_bank/reports/stale-context-$(date +%Y-%m-%d).md

# Check marker application if marking enabled
if [[ "$MARK_MODE" == "true" ]]; then
  grep -r "<!-- STALE:" .claude/memory_bank --exclude-dir=archive | wc -l
fi

# Review git diff impact
git diff --stat .claude/memory_bank/
```

---

## 6) Expected Outcomes

### Typical Stale Context Detection Results

- **Comprehensive Issue Identification** - All stale, contradictory, or obsolete content flagged with specifics
- **Non-Destructive Marking** - Optional inline markers guide reviewers without disrupting content
- **Actionable Reporting** - Detailed report with file locations, reasons, and next steps for each issue
- **Version Control Friendly** - Minimal diffs and clean change tracking for review workflows
- **Prioritized Remediation** - Issues categorized by severity and type for efficient maintenance planning

### Success Metrics

- Number of stale content issues identified by category (missing code, conflicts, outdated)
- Accuracy of detection (true positives vs false positives in manual review)
- Report quality metrics (actionability, specificity, helpful guidance)
- Marking effectiveness (clear guidance without content disruption)

---

## 7) Quality Assurance

### Content Preservation Checklist

- [ ] No original content deleted or corrupted during detection process
- [ ] All memory bank files remain functional and readable after marking
- [ ] Archive directory completely excluded from scanning and marking
- [ ] Inline markers clearly distinguishable but non-disruptive to content flow
- [ ] Version control diffs minimal and targeted to actual issues found

### Detection Accuracy Checklist

- [ ] Code reference validation accurately identifies missing files and functions
- [ ] Pattern conflict detection compares against current implementation correctly
- [ ] Git baseline comparison uses appropriate commit references
- [ ] Issue categorization properly distinguishes between severity levels
- [ ] Report recommendations specific and actionable for each identified problem

---

## 8) Post-Implementation Maintenance

### Regular Stale Context Detection Schedule

- **Weekly**: Run stale context check after significant development cycles or major features
- **Monthly**: Comprehensive check with broader repository scope to catch gradual staleness
- **After Major Releases**: Full validation following significant architecture or technology changes
- **Before Important Milestones**: Ensure documentation accuracy before demos, audits, or reviews

### Warning Signs for Memory Bank Staleness

- Increasing number of stale content issues across multiple detection runs
- Frequent false positives indicating detection logic needs refinement
- Developer feedback about outdated or misleading memory bank guidance
- Repository restructuring or technology changes not reflected in documentation

---

## 9) Documentation Standards

### Stale Context Report Format

```markdown
# Stale Context Detection Report - YYYY-MM-DD

**Scan Scope**: [memory bank files analyzed]
**Repository Baseline**: [git commit reference]
**Generated**: [ISO timestamp]
**Parameters**: --paths=[filters] --mark=[true/false] --since=[git-ref]
**Total Issues**: [count by category]

---

## Detection Summary
- **Missing Code References**: [count] issues found
- **Implementation Conflicts**: [count] issues found  
- **Outdated Decisions**: [count] issues found
- **Aged Content**: [count] sections requiring validation

---

## High Priority Issues

### Missing Code References
1. **File**: .claude/memory_bank/patterns/user-auth.md:45
   - **Issue**: References `src/auth/removeUser.js` (removed in commit abc123)
   - **Action**: Update pattern to reference current `src/auth/userManagement.js`
   - **Marker**: `<!-- STALE: verify against repo commit abc123 -->`

2. **File**: .claude/memory_bank/architecture/api-design.md:123  
   - **Issue**: Function `validateRequest()` not found in current codebase
   - **Action**: Verify function location or update documentation
   - **Marker**: `<!-- STALE: function validateRequest() not found -->`

### Implementation Conflicts
1. **File**: .claude/memory_bank/decisions/database-layer.md:67
   - **Issue**: Documented ORM approach conflicts with current implementation
   - **Action**: Review decision relevance against commit def456 changes
   - **Marker**: `<!-- STALE: pattern conflicts with current implementation -->`

2. **File**: .claude/memory_bank/troubleshooting/payment-timeout.md:31
   - **Issue**: Runbook steps cite deprecated queues/alerts; fix versions unspecified
   - **Action**: Revalidate diagnostics and fixes; note affected versions and current SLO/alerts
   - **Marker**: `<!-- STALE: verify against recent implementation changes -->`
---

## Medium Priority Issues

### Aged Content Requiring Validation
1. **File**: .claude/memory_bank/patterns/error-handling.md:12
   - **Issue**: Last validated 15 commits ago, needs current verification  
   - **Action**: Review against recent error handling changes
   - **Marker**: `<!-- STALE: verify against recent implementation changes -->`

---

## Detection Statistics
- **Files Scanned**: [count]
- **References Validated**: [count]
- **Markers Applied**: [count] (if --mark enabled)
- **False Positives**: [estimated count] - review needed
- **Processing Time**: [duration]

---

## Recommended Actions
1. **Immediate**: Address high-priority missing code references
2. **This Week**: Resolve implementation conflicts with current codebase
3. **This Month**: Validate aged content against recent changes
4. **Ongoing**: Establish regular stale context detection in development workflow
```

### Inline Marker Standards

```markdown
<!-- STALE: verify against repo commit abc123 - src/utils/helpers.js removed -->
# Database Helper Patterns

This section describes patterns for database helpers located in `src/utils/helpers.js`...

---

<!-- STALE: function getUserById() not found in current codebase -->
## User Retrieval Pattern

Use the `getUserById()` function to safely retrieve user data...

---

<!-- STALE: pattern conflicts with current implementation - see commit def456 -->
## Authentication Flow

The current authentication pattern follows these steps...
```

---

## Agent Prompting Tips (how to use this command)

### Command Usage Patterns
- **Full Analysis**: `/stale-context-check` - Complete memory bank validation with default marking
- **Scoped Check**: `/stale-context-check --paths="src/auth/**,packages/web/**"` - Limit to specific areas
- **Report Only**: `/stale-context-check --no-mark` - Generate report without inline markers
- **Historical Baseline**: `/stale-context-check --since=origin/main` - Compare against specific commit

### Detection Strategy Guidelines
- **Recent Changes Focus**: Use --since parameter after major refactoring or feature development
- **Targeted Analysis**: Apply --paths filter when working on specific system components
- **Non-Disruptive Mode**: Use --no-mark for read-only analysis or when sharing repositories
- **Comprehensive Review**: Run without filters periodically for complete memory bank health check

### Review Workflow Integration
- **Pre-Review**: Run stale context check before code reviews to ensure documentation accuracy
- **Post-Deployment**: Validate memory bank after significant releases or architecture changes
- **Documentation Maintenance**: Use reports to systematically update and maintain memory bank content
- **Team Coordination**: Share reports to distribute documentation maintenance across team members

### Interpreting Results Effectively
- **Prioritize by Impact**: Address missing code references first, then implementation conflicts
- **Validate False Positives**: Review flagged items that may be intentionally preserved for historical context
- **Batch Similar Issues**: Group related problems for efficient resolution (e.g., all references to moved files)
- **Track Trends**: Monitor detection patterns over time to identify systemic documentation maintenance needs

### Troubleshooting Detection Issues
- **High False Positives**: Refine detection patterns or adjust git baseline comparison
- **Missed Stale Content**: Broaden repository scan scope or review detection logic for edge cases
- **Marker Conflicts**: Ensure marking format doesn't conflict with existing comment patterns
- **Performance Issues**: Use --paths filter to limit scope for large repositories or memory banks