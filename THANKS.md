# Thanks and Attribution

## Inspiration and Foundation

This memory bank system is built upon the excellent foundational work by **centminmod** and their [`my-claude-code-setup`](https://github.com/centminmod/my-claude-code-setup) repository. We are grateful for their open-source sharing.

## Conceptual Inspirations

### Core Philosophy
The **centminmod** repository introduced several key concepts that fundamentally shaped our thinking:

- **Persistent Context Management**: The idea that Claude sessions should maintain project knowledge between interactions rather than starting fresh each time
- **Specialized Agent Architecture**: Using purpose-built AI agents for specific development tasks instead of generic prompts

### System Architecture Insights
Their work demonstrated how to:
- Structure agent definitions with clear capabilities and use cases
- Design prompts that maintain consistency across different coding contexts
- Create reusable patterns for common development tasks
- Balance automation with developer control

## How We Built Upon This Foundation

### Direct Adoptions
We directly adopted and adapted these components from the original repository:

#### **Code Searcher Agent** (`agents/code-searcher.md`)
- **Original Source**: [agents/code-searcher.md](https://github.com/centminmod/my-claude-code-setup/blob/main/agents/code-searcher.md)

#### **UX Design Expert Agent** (`agents/ux-design-expert.md`)
- **Original Source**: [agents/ux-design-expert.md](https://github.com/centminmod/my-claude-code-setup/blob/main/agents/ux-design-expert.md)

#### **Core CLAUDE.md Structure**
- **Original Source**: [CLAUDE.md](https://github.com/centminmod/my-claude-code-setup/blob/main/CLAUDE.md)
- **Our Adaptation**: Extended with memory bank system instructions, context selection priorities, and granular file management workflows

### Major Extensions and Innovations

#### **Memory Bank System**
While inspired by centminmod's context management ideas, we developed a comprehensive memory bank architecture:
- **Granular Context Files**: `decisions/`, `patterns/`, `architecture/`, `troubleshooting/`
- **Archive System**: Historical snapshots with read-only preservation
- **Delta-Based Updates**: Incremental synchronization for efficiency
- **Command Interface**: `/context-query`, `/update-memory-bank`, `/cleanup-context`

#### **Specialized Context Agents**
We created additional agents that build on centminmod's agent pattern:
- **`memory-bank-synchronizer`**: Keeps documentation aligned with code reality
- **`context-query-agent`**: Just-in-time context retrieval with scoped access
- **`context-diff-agent`**: Delta detection between repository states
- **`stale-context-agent`**: Identifies and flags outdated documentation

#### **Development Workflow Integration**
Extended beyond configuration into complete development lifecycle support:
- Feature development workflows with context tracking
- Bug investigation patterns with historical knowledge
- Code review preparation with context bundling
- Project hygiene automation with staleness detection

## Finding Updated Versions

### Original Repository
For the latest versions of the foundational components:
- **Main Repository**: https://github.com/centminmod/my-claude-code-setup
- **Code Searcher**: https://github.com/centminmod/my-claude-code-setup/blob/main/agents/code-searcher.md
- **UX Design Expert**: https://github.com/centminmod/my-claude-code-setup/blob/main/agents/ux-design-expert.md
- **Base CLAUDE.md**: https://github.com/centminmod/my-claude-code-setup/blob/main/CLAUDE.md

### Our Extensions
Our extended system with memory banking and additional agents:
- **This Repository**: https://github.com/russbeye/claude-memory-bank
- **Extended Agents**: `agents/` directory in this repository
- **Memory Bank Commands**: `commands/context/` directory
- **Workflow Guides**: `workflows/memory-bank/` directory

## Contributing Back

We encourage users of both systems to:
- Contribute improvements back to the original centminmod repository
- Share novel agent patterns and workflows with both communities
- Document successful integration patterns for cross-pollination of ideas