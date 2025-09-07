# Workflow: PR Authoring with JIT Context

**Intent:** Generate the minimal set of decisions/patterns/architecture notes needed to **review or author** a PR.  
**Primary Benefit:** Extremely small context windows for reviews and change descriptions.

## Visual Workflow

```mermaid
flowchart TD
    A["/context-diff command"] --> B["context-diff-agent"]
    B --> C["Updates granular memory bank"]
    
    D["/context-query command"] --> E["context-query-agent"]
    E --> F["Generates focused context bundle"]
    
    G["/stale-context-check command"] --> H["stale-context-agent"]
    H --> I["Validates and flags stale content"]
    
    A --> |"Optional: if granular bank not up-to-date"| D
    F --> |"Optional fallback: if bundle has stale guidance"| G
```

## Triggers
- Opening a PR
- Requesting review context for a specific module

## Preconditions
- Granular memory bank is up-to-date (if not, run `/context-diff` first)

## Steps
1) **Produce a scoped context bundle**
   - Run: `/context-query "<path-or-feature>" --include=decisions,architecture --limit=8 --format=bundle`
   - Attach or paste bundle into PR description as needed

2) **(Optional) Tighten further**
   - Restrict to a subpath: `/context-query src/payments/** --limit=5`

## Fallbacks
- If the bundle references stale guidance, run `/stale-check --paths="src/payments/**" --no-mark` and refresh

## Success Criteria
- Bundle fits within a small token budget
- Reviewers have just what they need (no noise)
