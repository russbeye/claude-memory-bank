# Context Query Command (for AI Agent)

## 0) Metadata
- **Command Title:** Context Query - Just-in-Time Memory Bank Retrieval
- **Command Type:** query/retrieval
- **Target Domain:** memory bank files, contextual information extraction
- **Complexity Level:** simple
- **Expected Duration:** 1-3 minutes depending on query complexity and memory bank size
- **Dependencies:** memory bank directory structure, grep/search capabilities

---

## 1) Command Summary
**Objective:** Produce a minimal, just-in-time context bundle for a module/feature by querying granular memory docs. This does not modify any memory-bank files; it emits a temporary slice for the current task/PR.  
**Success Metrics:** Result contains only sections needed for the query, bundle is succinct and de-duplicated, ready for prompt/PR integration  
**Scope:** Read-only querying of memory bank files, cross-link resolution, temporary context file generation  
**Business Impact:** Enables focused context retrieval without full memory bank overhead, accelerates development workflow  

---

## 2) Analysis Phase
### 2.1 Initial Assessment

```bash
# Check memory bank structure and availability
ls -la .claude/memory_bank/decisions/
ls -la .claude/memory_bank/patterns/
ls -la .claude/memory_bank/architecture/
ls -la .claude/memory_bank/troubleshooting/

# Review existing context queries for patterns
ls -la .claude/memory_bank/reports/context-query-*.md

# Examine query target areas
find .claude/memory_bank -name "*.md" -not -path "*/archive/*" | head -20
```

**Examine for:**

- Available memory bank content organized by decisions, patterns, architecture, troubleshooting
- Query target scope (path globs, feature tags, free text patterns)
- Existing cross-references and related sections
- Memory bank file structure and content organization

### 2.2 Identify Query Opportunities

**High-Impact Targets (prioritize first):**

- Core architecture files for system structure understanding
- Decision documents for specific features or components  
- Pattern files that match development context or coding standards
- Cross-linked sections that provide comprehensive feature context

**Medium-Impact Targets:**

- Related architectural decisions that inform the primary query
- Pattern files that provide supporting context for implementation
- Historical decisions that explain current system state
- Troubleshooting entries that surface known errors, and validated fixes
- Component-specific documentation that complements main query

**Low-Impact Targets:**

- Tangentially related files that don't directly address query intent
- Archived or deprecated information not relevant to current work
- Generic patterns that don't apply to specific query context
- Overly detailed documentation that exceeds current needs

---

## 3) Strategy & Approach

### Phase 1: Parse Query and Determine Scope (High Impact)

**Target:** Interpret query parameters and establish search strategy

**Actions:**

1. Parse `<query>` parameter for path globs, feature tags, or free text
2. Process optional flags: `--limit`, `--include`, `--format`
3. Determine search scope within memory bank directory structure
4. Validate query syntax and parameter compatibility

**Expected Results:** Well-defined search scope with validated parameters

### Phase 2: Search Memory Bank Files (High Impact)

**Target:** Execute comprehensive search across specified memory bank areas

**Common Search Pattern Opportunities:**

- **Path-based Queries:** `src/payments/**`, `packages/web/**` for component-specific context
- **Feature Queries:** `Feature: Checkout`, `Auth System` for functional area context  
- **Technical Queries:** `API Design`, `Database Schema` for technical decision context
- **Pattern Queries:** `Error Handling`, `State Management` for implementation pattern context

**Actions:**

1. Search `.claude/memory_bank/decisions/**` for architectural and technical decisions
2. Search `.claude/memory_bank/patterns/**` for coding conventions and practices
3. Search `.claude/memory_bank/architecture/**` for system structure information
4. Search `.claude/memory_bank/troubleshooting/**` for related issues and resolutions
5. Apply `--include` filter to limit search to specified categories

**Expected Results:** Comprehensive list of matching sections with relevance ranking

### Phase 3: Resolve Cross-Links and Related Sections (Medium Impact)

**Target:** Expand initial results with related and cross-referenced content

**Actions:**

1. Identify cross-references and related sections within matched content
2. Resolve links to ensure comprehensive context coverage
3. Apply `--limit` parameter to cap total number of sections included
4. Remove duplicate content and consolidate overlapping information

**Expected Results:** Curated set of relevant sections within specified limits

### Phase 4: Generate Context Bundle (Medium Impact)

**Target:** Emit formatted context bundle for immediate use

**Actions:**

1. Generate temporary context file with unique hash identifier
2. Write to `.claude/memory_bank/reports/context-query-<hash>.md`
3. Apply `--format` option (bundle file or inline return)
4. Include metadata about query parameters and matched sections
5. Ensure output is optimized for prompt or PR integration

**Expected Results:** Ready-to-use context bundle with complete metadata

---

## 4) Implementation Guidelines

### Query Parsing Patterns

**Query Type Recognition:**

```bash
# Path glob patterns
if [[ "$query" == *"/**" ]] || [[ "$query" == *"/*" ]]; then
  search_type="path_glob"
fi

# Feature tag patterns  
if [[ "$query" == "Feature:"* ]] || [[ "$query" == "feature:"* ]]; then
  search_type="feature_tag"
fi

# Free text patterns (default)
search_type=${search_type:-"free_text"}
```

**Quality Standards:**

- Parse all query parameters accurately without data loss
- Validate file paths and glob patterns before search execution
- Maintain case-insensitive search while preserving original content
- Handle special characters and escape sequences in query strings
- Provide clear error messages for invalid query syntax

### Memory Bank Search Conventions

- Use recursive grep with relevant file extensions (.md primarily)
- Respect `--include` parameter to limit search scope appropriately  
- Maintain search result ranking based on relevance and match quality
- Preserve original file structure and cross-reference links
- Exclude archive directory from all search operations

---

## 5) Execution Process

### 1. Plan and Validate

```bash
# Validate memory bank structure
test -d .claude/memory_bank/decisions/
test -d .claude/memory_bank/patterns/  
test -d .claude/memory_bank/architecture/
test -d .claude/memory_bank/troubleshooting/

# Parse and validate query parameters
echo "Query: $query"
echo "Limit: ${limit:-20}"
echo "Include: ${include:-decisions,patterns,architecture,troubleshooting}"
echo "Format: ${format:-bundle}"
```

### 2. Execute by Priority

- **Query Parameter Processing:** Parse command line arguments and validate syntax
- **Memory Bank Scanning:** Execute searches across specified directory categories
- **Cross-Reference Resolution:** Follow links and related sections within limits
- **Bundle Generation:** Create formatted output according to format specification

### 3. Update References

- Write temporary context bundle to reports directory with unique identifier
- Log query execution with parameters and result summary
- Update query history or cache if implementing optimization features
- Maintain clean temporary file management

### 4. Validate Results

```bash
# Verify bundle generation
test -f .claude/memory_bank/reports/context-query-$(echo "$query" | md5sum | cut -d' ' -f1).md

# Check bundle size and content
wc -l .claude/memory_bank/reports/context-query-*.md
head -10 .claude/memory_bank/reports/context-query-*.md

# Validate format compliance
grep -c "^# " .claude/memory_bank/reports/context-query-*.md
```

---

## 6) Expected Outcomes

### Typical Context Query Results

- **Focused Context Retrieval** - Only relevant sections included in output bundle
- **Efficient Information Access** - Quick retrieval without full memory bank processing
- **Ready Integration Format** - Output optimized for immediate use in prompts or PRs
- **Cross-Reference Completeness** - Related sections included for comprehensive context
- **Minimal Noise** - De-duplicated and consolidated information without redundancy

### Success Metrics

- Number of relevant sections identified and included in bundle
- Query execution time and search performance
- Bundle size optimization (relevant content vs total size ratio)
- Successful cross-reference resolution and link following

---

## 7) Quality Assurance

### Content Quality Checklist

- [ ] Query results directly address the specified search parameters
- [ ] All cross-references and related sections properly resolved
- [ ] No duplicate or redundant information included in bundle
- [ ] Bundle size stays within reasonable limits for practical use
- [ ] Content formatting preserved for readability and integration

### Process Compliance Checklist

- [ ] Read-only operation - no modifications to memory bank files
- [ ] Archive directory completely excluded from search results
- [ ] Optional parameters (--limit, --include, --format) honored correctly
- [ ] Temporary files created with proper naming and location
- [ ] Error handling for invalid queries or missing memory bank structure

---

## 8) Post-Implementation Maintenance

### Regular Query Optimization Schedule

- **Weekly**: Review query patterns and optimize commonly used searches
- **Monthly**: Clean up temporary context query files older than 30 days
- **Quarterly**: Analyze query performance and consider indexing improvements
- **As-needed**: Update query parsing logic for new memory bank organizational patterns

### Warning Signs for Query System Issues

- Query execution times consistently exceeding expected duration ranges
- Frequent empty or irrelevant results for valid queries  
- Memory bank structure changes breaking existing query patterns
- Temporary file accumulation causing storage or performance issues

---

## 9) Documentation Standards

### Context Query Bundle Format

```markdown
# Context Query Results - [Query Hash]

**Query**: [original query string]
**Generated**: [ISO timestamp]
**Parameters**: --limit=[n] --include=[categories] --format=[type]
**Sections Found**: [count]

---

## Query Summary
- **Search Type**: [path_glob/feature_tag/free_text]
- **Categories Searched**: [decisions/patterns/architecture/troubleshooting]
- **Cross-References**: [count resolved]

---

## [Section 1: Category/File Name]
### [Subsection Title]
[Relevant content from memory bank file]

**Source**: .claude/memory_bank/[category]/[filename].md
**Relevance**: [High/Medium/Low]

---

## [Section 2: Category/File Name] 
### [Subsection Title]
[Relevant content from memory bank file]

**Source**: .claude/memory_bank/[category]/[filename].md  
**Cross-References**: [Related sections]

---

## Related Sections (Cross-Referenced)
### [Related Section Title]
[Brief excerpt or summary of related content]

**Source**: [file path]
**Relationship**: [how it relates to main query]
```

### Query History Format

```json
{
  "query": "src/payments/**",
  "timestamp": "2024-01-15T10:30:00Z",
  "parameters": {
    "limit": 20,
    "include": "decisions,patterns,troubleshooting",
    "format": "bundle"
  },
  "results": {
    "sections_found": 8,
    "cross_references": 3,
    "bundle_file": "context-query-abc123.md"
  },
  "execution_time_ms": 1250
}
```

---

## Agent Prompting Tips (how to use this command)

### Query Construction Best Practices
- **Path Queries**: Use specific glob patterns like `src/auth/**` rather than overly broad patterns
- **Feature Queries**: Start with `Feature:` prefix for functional area searches like `Feature: Checkout`  
- **Technical Queries**: Use descriptive terms like `API Design` or `Database Schema` for technical context
- **Scoped Searches**: Combine path patterns with include filters for focused results

### Parameter Usage Guidelines
- **--limit**: Set reasonable limits (10-30) to avoid overwhelming context bundles
- **--include**: Use specific categories when you need focused information (e.g., just decisions)
- **--format=inline**: Use for immediate consumption; --format=bundle for reusable context files
- **Query Refinement**: Start broad, then narrow with additional parameters if needed

### Integration Patterns
- **Development Context**: Query specific components before making changes
- **PR Context**: Generate focused bundles for code review context
- **Documentation**: Extract relevant patterns and decisions for documentation writing
- **Troubleshooting**: Query related decisions and architecture for debugging context

### Troubleshooting Query Issues
- **Empty Results**: Broaden query terms or check memory bank content availability
- **Too Many Results**: Add --limit parameter or use --include to narrow scope
- **Irrelevant Results**: Use more specific path patterns or feature tags
- **Missing Cross-References**: Verify memory bank files contain proper internal linking