# Context Diff Command (for AI Agent)

## 0) Metadata
- **Command Title:** Context Diff - Delta Memory Bank Synchronization
- **Command Type:** analysis/synchronization
- **Target Domain:** memory bank files, git repository changes
- **Complexity Level:** medium
- **Expected Duration:** 5-15 minutes depending on repository size
- **Dependencies:** git, context-diff-agent, memory bank directory structure

---

## 1) Command Summary
**Objective:** Run a delta-only synchronization of the memory bank by invoking the `context-diff-agent`. Only files that changed since the last run will trigger updates in granular memory docs.  
**Success Metrics:** Only changed areas reflected in updated granular docs, complete report generated, baseline cache updated  
**Scope:** Git repository changes analysis, selective memory bank updates, progress tracking and reporting  
**Business Impact:** Efficient memory bank maintenance without full regeneration, preserves development context accuracy  

---

## 2) Analysis Phase
### 2.1 Initial Assessment

```bash
# Check current git status and recent changes
git status
git log --oneline -10

# Examine existing diff cache
cat .claude/memory_bank/.diff-cache.json

# Review memory bank structure  
ls -la .claude/memory_bank/decisions/
ls -la .claude/memory_bank/patterns/
ls -la .claude/memory_bank/architecture/
ls -la .claude/memory_bank/troubleshooting/
```

**Examine for:**

- Repository changes since last context-diff run
- Modified files that impact memory bank areas
- Current baseline commit in diff cache
- Existing memory bank file structure and timestamps

### 2.2 Identify Synchronization Opportunities

**High-Impact Targets (prioritize first):**

- Core architecture files in `src/`, `lib/`, `packages/` that define system structure
- New features or major refactoring in specified path filters
- Database schema, API contracts, or configuration changes
- Security, authentication, or authorization modifications

**Medium-Impact Targets:**

- Component updates that change patterns or conventions
- Test files that reveal new behavioral expectations  
- Documentation changes that reflect architectural decisions
- Build system or deployment configuration updates

**Low-Impact Targets:**

- Minor bug fixes or cosmetic changes
- Comment additions or formatting adjustments
- Temporary files or experimental code
- Files explicitly excluded by path filters

---

## 3) Strategy & Approach

### Phase 1: Determine Diff Base (High Impact)

**Target:** Establish the comparison baseline for git diff analysis

**Actions:**

1. Check for `--since` parameter and use if provided
2. Read `.claude/memory_bank/.diff-cache.json` for last run's commit
3. Validate that the baseline commit exists and is accessible  
4. Set fallback to origin/main if no valid baseline found

**Expected Results:** Valid git reference for comparison baseline

### Phase 2: Compute Repository Delta (High Impact)

**Target:** Identify all changed files within specified scope

**Common Path Filtering Opportunities:**

- **Source Code:** `src/**`, `lib/**`, `packages/**` for core application logic
- **Configuration:** `config/**`, `*.config.js`, environment files for system settings
- **Schema/Data:** Database migrations, API schemas, data model definitions
- **Tests:** Test files that reveal new behavioral patterns and requirements

**Actions:**

1. Execute `git diff --name-status <base>..HEAD` to get all changes
2. Apply `--paths` filter using CSV/glob patterns if provided
3. Categorize changes by file type and likely memory bank impact
4. Exclude archive directory and non-relevant file types

**Expected Results:** Filtered list of changed files with impact assessment

### Phase 3: Update Memory Bank Areas (High Impact)

**Target:** Selectively update memory bank documentation based on changes

**Actions:**

1. For each changed area, invoke **context-diff-agent** with specific scope
2. Update `.claude/memory_bank/decisions/**` for architectural choices
3. Update `.claude/memory_bank/patterns/**` for coding conventions and practices
4. Update `.claude/memory_bank/architecture/**` for system structure changes
5. Update `.claude/memory_bank/troubleshooting/**` for related troubleshooting guides

**Expected Results:** Updated memory bank files reflecting only relevant changes

### Phase 4: Generate Reports and Cache (Medium Impact)

**Target:** Document synchronization results and update tracking

**Actions:**

1. Generate comprehensive run summary with change details
2. Write report to `.claude/memory_bank/reports/context-diff-YYYY-MM-DD.md`
3. Update `.claude/memory_bank/.diff-cache.json` with new HEAD commit
4. Include metrics on files processed, areas updated, and time elapsed
5. Honor `--dry-run` by skipping all writes and showing report only

**Expected Results:** Complete audit trail and updated baseline for next run

---

## 4) Implementation Guidelines

### Git Operation Patterns

**Repository Analysis Pattern:**

```bash
# Determine baseline commit
BASELINE=$(cat .claude/memory_bank/.diff-cache.json | jq -r '.last_commit // "origin/main"')

# Get changed files with status
git diff --name-status $BASELINE..HEAD

# Filter by paths if specified  
git diff --name-status $BASELINE..HEAD -- src/service/auth/** packages/web/**

# Validate commit exists
git cat-file -e $BASELINE^{commit}
```

**Quality Standards:**

- Preserve all git history and references accurately
- Never modify files outside the memory bank structure
- Maintain atomic operations - complete success or clean rollback
- Include comprehensive error handling for git operations
- Validate all file paths before processing

### Memory Bank Update Conventions

- Use `-delta` suffix for incremental update files when needed
- Maintain consistent timestamp formatting in reports
- Preserve existing file structure and naming patterns
- Update cross-references between memory bank files when content changes

---

## 5) Execution Process

### 1. Plan and Validate

```bash
# Verify git repository state
git status

# Check memory bank structure exists
ls .claude/memory_bank/

# Validate diff cache or create if missing  
touch .claude/memory_bank/.diff-cache.json
```

### 2. Execute by Priority

- **Command Line Parsing:** Process `--since`, `--paths`, `--dry-run` parameters
- **Baseline Determination:** Establish valid git comparison point
- **Delta Computation:** Calculate and filter changed files
- **Memory Bank Updates:** Invoke context-diff-agent for relevant areas

### 3. Update References

- Write detailed report to timestamped file in reports directory
- Update diff cache with new baseline commit hash
- Refresh memory bank cross-references if structural changes occurred
- Log execution summary with timing and change metrics

### 4. Validate Results

```bash
# Confirm cache update
cat .claude/memory_bank/.diff-cache.json

# Verify report generation
ls -la .claude/memory_bank/reports/context-diff-$(date +%Y-%m-%d).md

# Check memory bank consistency
find .claude/memory_bank -name "*.md" -exec head -3 {} \;
```

---

## 6) Expected Outcomes

### Typical Context Diff Results

- **Selective Synchronization** - Only changed areas reflected in memory bank updates
- **Efficient Processing** - Skip unchanged files to minimize execution time
- **Complete Audit Trail** - Detailed report of all changes processed and decisions made
- **Baseline Tracking** - Updated cache enables incremental processing on subsequent runs
- **Memory Bank Integrity** - No corruption or loss of existing documentation

### Success Metrics

- Number of changed files identified and processed
- Count of memory bank files updated vs skipped  
- Execution time and performance metrics
- Successful baseline cache update with new commit hash

---

## 7) Quality Assurance

### Data Integrity Checklist

- [ ] Git operations complete successfully without errors
- [ ] All changed files within scope are identified and processed
- [ ] Memory bank updates preserve existing file structure and references
- [ ] No modifications made to archive directory or excluded areas
- [ ] Diff cache updated with valid, accessible commit hash

### Process Compliance Checklist

- [ ] Honor `--dry-run` flag by skipping all write operations
- [ ] Apply `--paths` filter correctly to limit scope
- [ ] Never delete existing granular memory bank files
- [ ] Generate timestamped report with complete change summary
- [ ] Maintain atomic operation - complete success or clean failure

---

## 8) Post-Implementation Maintenance

### Regular Context Diff Schedule

- **Daily**: Run context-diff after significant development sessions
- **Weekly**: Full context-diff without path filters to catch any missed changes  
- **After Major Releases**: Complete synchronization with explicit baseline reset
- **Before Important Demos**: Ensure memory bank reflects current system state

### Warning Signs for Re-synchronization

- Memory bank files significantly outdated compared to active development
- Diff cache contains invalid or inaccessible commit references
- Context-diff-agent reports errors during granular file updates
- Reports show consistently large numbers of changed files (indicates missed runs)

---

## 9) Documentation Standards

### Context Diff Report Format

```markdown
# Context Diff Report - YYYY-MM-DD

**Last Updated**: [ISO timestamp]
**Status**: ✅ COMPLETE - Delta Synchronization
**Coverage**: [Baseline commit] to [HEAD commit]

## Execution Summary
- **Files Changed**: [count] 
- **Memory Bank Updates**: [count]
- **Path Filters**: [applied filters or "none"]
- **Execution Time**: [duration]

## Changed Files Processed
### [Category - e.g., Architecture]
- **file/path.js** - [impact description]

## Memory Bank Updates
### [Updated Area]
[Description of changes made to memory bank files]

## Skipped Areas  
[Files/areas excluded and reasoning]
```

### Diff Cache Format

```json
{
  "last_commit": "abc123def456",
  "last_run": "2024-01-15T10:30:00Z", 
  "last_paths_filter": "src/**,lib/**",
  "run_count": 42
}
```

---

## Agent Prompting Tips (how to use this command)

### Command Usage Patterns
- **Basic Sync**: `/context-diff` - Use default baseline from cache
- **Manual Baseline**: `/context-diff --since=origin/main` - Specify comparison point
- **Scoped Updates**: `/context-diff --paths="src/auth/**,config/**"` - Limit to specific areas
- **Preview Mode**: `/context-diff --dry-run` - See what would change without updating

### Execution Best Practices
- **Run incrementally** rather than letting changes accumulate over long periods
- **Use path filters** for focused updates when working on specific system areas
- **Validate git state** before execution to ensure clean working directory
- **Review reports** to understand what changes were captured and processed

### Integration Guidelines  
- **Combine with regular development workflow** - ideal after feature completion or major refactoring
- **Coordinate with team** - ensure baseline commits are available to all developers
- **Monitor performance** - large repositories may benefit from more frequent smaller runs
- **Backup memory bank** before major synchronization operations

### Troubleshooting Common Issues
- **Invalid baseline**: Use `--since=HEAD~10` or specific commit to reset baseline
- **Path filter errors**: Verify glob patterns and file paths exist in repository
- **Agent failures**: Check context-diff-agent dependencies and memory bank directory permissions
- **Cache corruption**: Delete `.diff-cache.json` to force full baseline reset