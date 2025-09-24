# Specialized Agents

Powerful AI assistants designed for specific development tasks. These agents are optimized for their domains and work more efficiently than generic prompts for specialized work.

## Quick Agent Reference

## Agents at a glance

| When You Need  | Agent    | Primary Use | Speed  |
| -------------- | -------- | ----------- | ------ |
| **Find code/bugs** | `code-searcher` | Forensic code analysis, security scans (CoD) | Ultra-fast |
| **Execute & test code** | `code-executor` | Generate and run code to validate logic (CoC) | Fast |
| **Stepwise reasoning** | `code-thinker` | Sequential analysis, architecture tradeoffs, deep bug forensics (CoT) | Moderate |
| **Verify answers** | `code-verifier` | Draft → self-questions → revise for accuracy (CoVe) | Moderate |
| **Inject knowledge** | `code-knowledge` | Retrieve and apply standards & best practices (CoK) | Moderate |
| **Design/UX help** | `ux-design-expert` | UI/UX optimization, design systems | 2-3 min |
| **Current timestamp** | `get-current-datetime` | Get America/Chicago time | Instant |
| **Delta sync** | `context-diff-agent` | Incremental memory bank updates | 30 sec |
| **Focused context** | `context-query-agent` | Just-in-time context retrieval | 15 sec |
| **Full sync** | `memory-bank-synchronizer` | Complete memory bank refresh | 2-3 min |
| **Check accuracy** | `stale-context-agent` | Detect outdated documentation | 1 min |


## Core Agents

### `code-searcher` - Code Analysis & Bug Hunting
**Use for**: Finding specific code, security analysis, architectural understanding, debugging

**Key Features**:
* **Chain of Draft (CoD) Mode**: Ultra-concise analysis with 80-92% token reduction
* **Forensic Analysis**: Deep code investigation and security vulnerability detection
* **Pattern Detection**: Identify architectural patterns and consistency issues
* **Exact References**: Always provides file paths with line numbers

**Usage Examples**:
```text
# Standard analysis
"Where is the user authentication logic implemented?"
"Concise analysis of error handling patterns"

# Security analysis  
"Analyze authentication system for vulnerabilities using CoD"
"Find SQL injection risks in user input handling"
```

**CoD Mode Benefits**:

* 7-20% of normal token usage
* 50-75% faster responses
* Perfect for rapid codebase exploration
* Maintains 90-98% accuracy of standard mode

---

### `code-executor` - Code Validation & Testing
**Use for**: Generating and running code to validate logic, debug, or benchmark performance

**Key Features**:
* **Chain of Code (CoC) Mode**: Uses executable code as reasoning steps
* **Bug Reproduction**: Write minimal tests to replicate failures
* **Performance Benchmarking**: Measure algorithm efficiency with test harnesses
* **Validation**: Verify data flow and transformation correctness

**Usage Examples**:
```text
# Bug reproduction
"Reproduce null pointer exception in checkout flow"

# Performance
"Benchmark sort algorithm with 10k inputs"

# Data validation
"Check if transformations preserve required fields"
```

**CoC Mode Benefits**:
* Evidence-based conclusions via code execution
* Prevents speculation with verifiable outputs
* Minimal, non-destructive test snippets
* Ideal for debugging and optimization

---

### `code-thinker` - Sequential Reasoning & Deep Analysis
**Use for**: Breaking down complex issues into explicit steps, architectural analysis, root-cause debugging

**Key Features**:
* **Chain of Thought (CoT) Mode**: Transparent, step-by-step reasoning
* **Architecture Decisions**: Compare tradeoffs and design choices
* **Bug Forensics**: Identify root causes with logical progression
* **Performance Reasoning**: Explain complexity and bottlenecks clearly

**Usage Examples**:
```text
# Deadlock analysis
"Why do we get deadlocks during batch imports?"

# Architecture decision
"Which data structure fits LRU cache best?"
```

**CoT Mode Benefits**:
* Clear, sequential logic
* Easy to follow decision trails
* Focuses on reasoning over speculation
* Great for complex architectural and debugging tasks

---

### `code-verifier` - Accuracy & Safety Checks
**Use for**: Improving answer reliability through verification questions and revision

**Key Features**:
* **Chain of Verification (CoVe) Mode**: Draft → verify → revise
* **Error Reduction**: Systematic self-checking process
* **Question-driven**: Surfaces assumptions and weaknesses
* **Final Revision**: Produces improved, validated answers

**Usage Examples**:
```text
# Security risks
"Summarize authentication system security risks"

# Fix verification
"Is the payment bug fix safe?"
```

**CoVe Mode Benefits**:
* Increases accuracy via self-questioning
* Reduces overlooked assumptions
* Ensures safer, more reliable conclusions
* Produces stronger final outputs

---

### `code-knowledge` - Standards & Best Practices
**Use for**: Applying external standards, security practices, and industry benchmarks to code decisions

**Key Features**:
* **Chain of Knowledge (CoK) Mode**: Retrieve and apply authoritative knowledge
* **Standards Compliance**: OWASP, NIST, RFCs, etc.
* **Best Practices**: Integrates external guidance into recommendations
* **Knowledge Application**: Turns sources into concrete fixes

**Usage Examples**:
```text
# Password storage
"What’s the recommended password storage policy?"

# OAuth flow
"Harden our OAuth flow based on standards"
```

**CoK Mode Benefits**:
* Cites authoritative sources (OWASP, NIST, RFCs)
* Produces concrete, standards-based fixes
* Bridges code with external best practices
* Ensures compliance and future-proofing


### `ux-design-expert` - UI/UX Optimization
**Use for**: Interface design, user experience optimization, design systems, data visualization

**Key Features**:
- **Premium UI Design**: Create sophisticated, expensive-looking interfaces
- **UX Flow Optimization**: Streamline complex user journeys and reduce friction
- **Design Systems**: Build scalable component libraries with Tailwind CSS
- **Data Visualization**: Highcharts integration with responsive, accessible charts
- **Technical Implementation**: Production-ready code with accessibility compliance

**Usage Examples**:
```text
# UX optimization
"Our checkout process has too many steps and users are dropping off"
"Make this dashboard layout less confusing for users"

# Premium design
"Create a professional-looking component library that scales"  
"Design a sophisticated data visualization with Highcharts"

# Design systems
"Build a scalable button component system using Tailwind CSS"
```

### `context-diff-agent` - Delta Memory Bank Sync  
**Use for**: Efficient memory bank updates after code changes

**Key Features**:
- **Delta Processing**: Updates only changed areas using git diff
- **Token Efficient**: Scales with change volume, not codebase size
- **Smart Detection**: Uses git diff, file hashes, or timestamps  
- **Selective Updates**: Targets specific memory bank categories

**Usage Examples**:
```text
# After development work
"Code has been updated but memory bank is behind"
"Sync only the changes since last commit"

# Targeted updates  
"We've added new auth patterns but don't want to rebuild everything"
```

### `context-query-agent` - Just-in-Time Context
**Use for**: Getting focused context without loading entire memory bank

**Key Features**:
- **Surgical Precision**: Extract only relevant sections
- **Multiple Formats**: Inline, bundle, or report formats
- **Smart Filtering**: Include/exclude specific categories
- **Cross-Reference**: Automatically resolves related sections

**Usage Examples**:  
```text
# Development context
"Get context for the authentication module"
"Show me memory bank info for Feature: Checkout"

# Review context
"Generate focused context bundle for src/payments/**"
```

### `memory-bank-synchronizer` - Complete Refresh
**Use for**: Full memory bank updates, initial setup, major refactoring

**Key Features**:
- **Complete Alignment**: Ensures memory bank matches current codebase
- **Pattern Detection**: Identifies undocumented patterns and decisions
- **Cross-Reference**: Maintains links between memory bank files
- **Archive Safe**: Never modifies read-only archive files

**Usage Examples**:
```text  
# Major updates
"Our code has changed significantly but memory bank files are outdated"
"The patterns in memory bank don't match what we're actually doing"
```

### `stale-context-agent` - Accuracy Validation
**Use for**: Finding outdated or contradictory documentation

**Key Features**:
- **Smart Detection**: Identifies missing code references and conflicts
- **Non-Destructive**: Marks suspicious content without deleting
- **Actionable Reports**: Specific recommendations for each issue  
- **Categorized Issues**: Prioritizes high/medium/low impact problems

**Usage Examples**:
```text
# Accuracy checking
"Check if our memory bank has any stale or conflicting information"
"Find outdated references after the recent refactoring"
```

### `get-current-datetime` - Timestamp Utility  
**Use for**: Getting current timestamp in America/Chicago timezone

**Key Features**:
- **Fast**: Returns raw output instantly
- **Timezone Fixed**: Always America/Chicago
- **Multiple Formats**: Filename, readable, ISO formats

**Usage Examples**:
```text
"Get current timestamp"
"Current time for filename"  
"Get readable date format"
```

## Daily Workflow Integration

### Morning Development Routine
```text
# Get current context for your work area
→ context-query-agent: "Get context for src/feature/**"

# If you need to find specific code  
→ code-searcher: "Use CoD to locate user validation logic"
```

### During Active Development  
```text
# Quick code analysis (use CoD for speed)
→ code-searcher: "Brief analysis of error handling in payment flow"

# UX improvements
→ ux-design-expert: "Streamline this 5-step signup process"

# Stay current with changes
→ context-diff-agent: "Sync changes since last commit"
```

### End of Development Session
```text
# Full memory bank refresh after major changes
→ memory-bank-synchronizer: "Update memory bank with recent changes"

# Check for any accuracy issues
→ stale-context-agent: "Check for outdated content in updated areas"
```

### Weekly Maintenance
```text  
# Comprehensive accuracy check
→ stale-context-agent: "Full stale content detection"

# If issues found, refresh affected areas
→ memory-bank-synchronizer: "Sync memory bank with current code"
```

## Performance Tips

### Optimize Token Usage
- **Use CoD mode** with code-searcher for rapid analysis: "Use CoD to..."
- **Be specific** with context-query limits: "--limit=5-10"  
- **Target scope** with path filters: "src/auth/** only"

### Speed Optimization
- **code-searcher + CoD**: 80-92% faster, 7-20% token usage
- **context-diff-agent**: 80% faster than full memory bank sync
- **context-query-agent**: 15 seconds vs minutes for full context

### Accuracy Maintenance
- **Regular stale checks**: Weekly with stale-context-agent
- **Incremental sync**: Daily with context-diff-agent after changes
- **Full refresh**: Monthly or after major refactoring with memory-bank-synchronizer

## Best Practices

### Agent Selection
- **Code questions**: Always start with code-searcher (use CoD for speed)
- **Design questions**: Use ux-design-expert for anything UI/UX related
- **Memory bank tasks**: Match agent to scope (delta vs full vs query)
- **Multiple agents**: Chain them - analyze with code-searcher, then update with memory-bank-synchronizer

### Effective Prompting
- **Be specific**: "Find auth logic in src/services/" vs "find auth code"
- **Use keywords**: "CoD", "concise", "brief" trigger faster modes
- **Specify scope**: Include path patterns and limits to control output
- **Chain operations**: Use results from one agent to inform another

### Common Patterns
```text
# Investigation workflow
code-searcher (CoD) → context-query-agent → memory-bank-synchronizer

# Design workflow  
ux-design-expert → code-searcher (verify implementation) → context-diff-agent

# Maintenance workflow
stale-context-agent → memory-bank-synchronizer → context-query-agent (verify)
```

These agents transform specialized development tasks from lengthy manual processes into efficient, targeted operations that deliver exactly what you need when you need it.