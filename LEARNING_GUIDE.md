# Claude Code: Complete Learning Guide for AI Agent Development

## Table of Contents
1. [Executive Summary](#executive-summary)
2. [High-Level Architecture](#high-level-architecture)
3. [Core Components](#core-components)
4. [Learning Path](#learning-path)
5. [Implementation Details](#implementation-details)
6. [Advanced Features](#advanced-features)
7. [Best Practices](#best-practices)

---

## Executive Summary

Claude Code is an **agentic coding tool** that operates as a command-line interface (CLI) application, designed to help developers code faster through natural language interactions. Unlike traditional code completion tools, Claude Code functions as an autonomous development partner that can:

- **Understand entire codebases** through intelligent search and indexing
- **Execute terminal commands** with user permission
- **Manage Git workflows** and create pull requests
- **Connect to external tools** via Model Context Protocol (MCP)
- **Orchestrate multiple specialized sub-agents** for complex tasks

### Key Characteristics
- **Local Execution**: Runs entirely in your terminal, no remote server required
- **Direct API Communication**: Connects directly to Anthropic's Claude API
- **Permission-Based Actions**: Asks for consent before making changes
- **Context-Aware**: Maintains awareness of your entire project structure

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      User Terminal                           │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌─────────────────┐      ┌──────────────────────┐          │
│  │  Claude Code    │◄────►│  Anthropic Claude    │          │
│  │  CLI Client     │      │  API (Opus/Sonnet)   │          │
│  └────────┬────────┘      └──────────────────────┘          │
│           │                                                   │
│           ▼                                                   │
│  ┌─────────────────────────────────────────────┐            │
│  │         Core Components Layer                │            │
│  ├─────────────────────────────────────────────┤            │
│  │ • File System Manager                        │            │
│  │ • Command Executor                           │            │
│  │ • Context Manager                            │            │
│  │ • Permission Handler                         │            │
│  └─────────────────────────────────────────────┘            │
│           │                                                   │
│           ▼                                                   │
│  ┌─────────────────────────────────────────────┐            │
│  │         Extension Layer                      │            │
│  ├─────────────────────────────────────────────┤            │
│  │ • MCP Servers (Tools & Data Sources)         │            │
│  │ • Sub-Agents (Specialized AI Assistants)    │            │
│  │ • Custom Commands (/slash commands)          │            │
│  └─────────────────────────────────────────────┘            │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow
1. **User Input** → Natural language command in terminal
2. **Claude Code Processing** → Parses intent, gathers context
3. **API Communication** → Sends context + query to Claude API
4. **Response Processing** → Claude generates plan/code
5. **Action Execution** → With permission, executes changes
6. **Feedback Loop** → Results inform next actions

---

## Core Components

### 1. CLI Application Core (`@anthropic-ai/claude-code`)
The main npm package that provides the command-line interface.

**Key Responsibilities:**
- Session management
- User interaction handling
- API communication orchestration
- File system operations
- Command execution with permission checks

**Installation:**
```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Context Management System
Maintains awareness of your project structure and relevant files.

**Components:**
- **Codebase Mapper**: Analyzes project structure
- **Dependency Tracker**: Understands imports and relationships
- **File Indexer**: Intelligent file search and retrieval
- **Context Window Manager**: Optimizes token usage

### 3. Permission System
Ensures safe operation by requesting user consent.

**Permission Types:**
- File modifications (read/write/delete)
- Command execution
- Git operations
- Network requests (via MCP)

### 4. Model Context Protocol (MCP)
Universal protocol for connecting to external tools and services.

**Architecture:**
```
Claude Code (Host/Client) ←→ MCP Server ←→ External Tool/API
```

**Categories of MCP Servers:**
- **Core Utilities**: File system, memory, sequential thinking
- **Development Tools**: GitHub, GitLab, Jira, Linear
- **Data Sources**: Databases, APIs, cloud services
- **Communication**: Slack, email, notifications

### 5. Sub-Agent System
Specialized AI assistants for specific tasks.

**Structure:**
```yaml
---
name: agent-name
description: Activation criteria
model: haiku|sonnet|opus  # Optional model selection
tools: tool1, tool2       # Restricted tool access
---

# System prompt defining expertise
```

**Key Features:**
- Independent context windows
- Task-specific expertise
- Tool access control
- Inter-agent communication

---

## Learning Path

### Stage 1: Foundation (Beginner)
**Goal**: Understand basic Claude Code usage

1. **Installation & Setup**
   - Install Claude Code globally
   - Configure API key
   - Run first session

2. **Basic Commands**
   - Natural language requests
   - File operations
   - Simple code generation

3. **Understanding Permissions**
   - Permission prompts
   - Safety mechanisms
   - `--dangerously-skip-permissions` flag (use with caution)

### Stage 2: Configuration (Intermediate)
**Goal**: Customize Claude Code for your workflow

1. **Project Configuration**
   - `.claude/` directory structure
   - `CLAUDE.md` for project context
   - Settings files (`.claude.json`, `settings.local.json`)

2. **Custom Commands**
   - Creating `/slash` commands
   - Command templates with `$ARGUMENTS`
   - Storing in `.claude/commands/`

3. **Model Selection**
   - Understanding model differences (Opus vs Sonnet vs Haiku)
   - `/model` command usage
   - Cost-performance tradeoffs

### Stage 3: MCP Integration (Advanced)
**Goal**: Extend capabilities with external tools

1. **MCP Fundamentals**
   - Protocol architecture
   - Server types (stdio, HTTP, SSE)
   - Authentication methods

2. **Implementing MCP Servers**
   ```javascript
   // Basic MCP server structure
   import { Server } from "@modelcontextprotocol/sdk/server/index.js";
   
   const server = new Server({
     name: "custom-server",
     version: "1.0.0"
   }, {
     capabilities: {
       resources: {},
       tools: {}
     }
   });
   ```

3. **Popular MCP Integrations**
   - GitHub/GitLab
   - Databases (PostgreSQL, MongoDB)
   - Cloud services (AWS, GCP)
   - Communication tools (Slack, Discord)

### Stage 4: Sub-Agents (Expert)
**Goal**: Orchestrate multi-agent workflows

1. **Creating Sub-Agents**
   - Agent definition files
   - Specialized prompts
   - Tool restrictions

2. **Agent Coordination**
   - Sequential workflows
   - Parallel execution
   - Context sharing strategies

3. **Complex Workflows**
   - Test-driven development patterns
   - Multi-repository operations
   - Full-stack feature implementation

---

## Implementation Details (Updated with Runtime Analysis)

### Core Design Philosophy: Simplicity Over Complexity

Based on runtime analysis from reverse engineering efforts, Claude Code follows a **"Keep Things Simple"** philosophy with these confirmed patterns:

### Actual Control Flow (from Runtime Analysis)

The main loop follows this sequence:

1. **Quota Check** (Haiku 3.5) - Lightweight check for available API quota
2. **Topic Detection** (Haiku 3.5) - Determines if new topic (for UI updates)
3. **System Reminder Injection** - Adds context before/after user messages
4. **Core Workflow** (Sonnet 4) - Main processing loop
5. **Context Compaction** (Sonnet 4) - When context gets too large
6. **Todo Management** - JSON files in `~/.claude/todos/`
7. **Sub-Agent Spawning** - For independent tasks (via Task tool)
8. **Session Summarization** (Haiku 3.5) - For next conversation

### Model Usage Distribution (Confirmed)
- **Haiku 3.5**: Quota checks, topic detection, summarization (~50%+ of calls)
- **Sonnet 4**: Core workflow, context compaction, main reasoning
- **Opus 4.1**: Complex planning (less frequently used than expected)

### Sub-Agent Architecture (Confirmed Design)

Key insights about the sub-agent system:
- **Main Agent Concept**: Always maintains a primary agent
- **Task Extraction**: Sub-agents receive extracted tasks as initial prompts
- **Result Integration**: Sub-agent results return as tool results to main
- **Context Isolation**: "Dirty context" (failed searches, irrelevant reads) stays in sub-context
- **Maximum Depth**: Single branch (depth=1) to maintain simplicity

### Todo Memory System

Claude Code implements short-term memory through:
- JSON files stored in `~/.claude/todos/`
- TodoWrite tool creates/updates these files
- System reminder prompts check and load todos
- Provides persistence across conversation turns

### Control Loop Architecture

```
Main Thread (Single Message History)
    ├── Simple Tasks → Direct Tool Calls
    └── Complex Tasks → Task Tool → Sub-Agent (Max 1 Branch)
                                      └── Returns final result only
```

**Key Insight**: The sub-agent pattern is primarily for context optimization, not parallel processing. It isolates exploratory operations that generate lots of context noise.

### File Structure
```
your-project/
├── .claude/
│   ├── agents/           # Sub-agent definitions
│   ├── commands/         # Custom slash commands
│   ├── settings.local.json  # Local configuration
│   └── CLAUDE.md        # Project context (1000-2000 tokens typically)
├── .mcp.json            # MCP server configuration
└── your-code/
```

### Prompt Engineering Details

**System Prompt Structure** (~2,800 tokens):
- Tone and Style guidelines
- Proactiveness rules
- Task Management algorithms
- Tool Usage Policy
- Code Style conventions
- Platform/OS information
- Current date and working directory

**Tool Descriptions** (~9,400 tokens):
- Elaborate descriptions with examples
- Good/bad example patterns
- When to use each tool
- Tool selection heuristics

### Configuration Examples

**System Prompt XML Tags Usage:**
```xml
<system-reminder>
This is a reminder that your todo list is currently empty. 
DO NOT mention this to the user explicitly.
</system-reminder>

<good-example>
pytest /foo/bar/tests
</good-example>

<bad-example>
cd /foo/bar && pytest tests
</bad-example>
```

**Steering Techniques:**
```markdown
# IMPORTANT: You MUST avoid using search commands like `find` and `grep`
# VERY IMPORTANT: ALWAYS USE ripgrep at `rg` first
# NEVER generate or guess URLs unless confident
```

**API Configuration:**
```bash
# Environment variable
export ANTHROPIC_API_KEY="sk-ant-..."

# Or in shell profile
echo 'export ANTHROPIC_API_KEY="sk-ant-..."' >> ~/.bashrc
```

**MCP Configuration (`.mcp.json`):**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_..."
      }
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ]
    }
  }
}
```

**Sub-Agent Example (`~/.claude/agents/code-reviewer.md`):**
```markdown
---
name: code-reviewer
description: Analyzes code for best practices and potential issues
tools: filesystem, git
---

You are an expert code reviewer focused on:
- Code quality and maintainability
- Security vulnerabilities
- Performance optimization
- Best practices adherence

Always provide constructive feedback with specific examples.
```

---

## Advanced Features

### 1. Tool Design Strategy

Claude Code implements a **three-tier tool hierarchy**:

**Low-Level Tools:**
- `Bash`: Direct command execution
- `Read/Write`: File operations
- Basic Unix commands

**Medium-Level Tools:**
- `Edit`: Targeted file modifications
- `Grep/Glob`: Optimized search operations
- `LS`: Directory listing with context

**High-Level Tools:**
- `Task`: Sub-agent spawning
- `WebFetch`: Complete web page retrieval
- `TodoWrite/TodoRead`: Task management
- `exit_plan_mode`: Session management

**Tool Usage Frequency** (from analysis):
1. Edit (most frequent)
2. Read
3. TodoWrite
4. Grep/Glob
5. Bash (when specialized tools don't fit)

### 2. Search Implementation: LLM vs RAG

Claude Code **rejects traditional RAG** in favor of LLM-powered search:

```bash
# Claude Code uses sophisticated regex with ripgrep
rg "function.*authenticate" --type js -A 5 -B 5

# Complex find commands
find . -type f -name "*.py" -exec grep -l "import torch" {} \;

# JSON structure analysis
jq '.dependencies | keys' package.json | head -10
```

**Why LLM Search > RAG:**
- No hidden failure modes (chunking, similarity functions)
- Inspectable and debuggable commands
- RL-learnable patterns
- Mimics human search behavior
- Handles diverse file types naturally

### 3. Model Optimization Strategy

**50%+ of calls use Claude 3.5 Haiku for:**
- Reading large files
- Parsing web pages
- Processing git history
- Summarizing conversations
- Generating UI labels ("processing", "thinking", etc.)

**Cost Optimization:**
- Haiku is 70-80% cheaper than Sonnet/Opus
- Use liberally for non-critical tasks
- Reserve Opus for complex reasoning and planning

### 4. Todo List Management

Claude Code's self-managed todo list serves multiple purposes:
- **Prevents context rot** in long sessions
- **Maintains focus** on original objectives
- **Enables course correction** mid-implementation
- **Leverages interleaved thinking** for dynamic planning

```markdown
## Current Tasks
- [x] Analyze authentication flow
- [ ] Implement JWT validation
- [ ] Add rate limiting
- [ ] Write tests for auth endpoints
```

### 5. Headless Mode
Run Claude Code without interactive prompts:
```bash
claude -p "Fix all linting errors" --output-format stream-json
```

### 6. Multi-Agent Orchestration
```bash
# Sequential execution
"Use backend-architect to design API, then frontend-developer to build UI"

# Parallel execution with coordination
"Have security-auditor and performance-engineer review simultaneously"
```

### 7. GitHub Actions Integration
Automate workflows with Claude Code in CI/CD:
```yaml
- name: Claude Code Analysis
  uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    prompt: "Review PR and suggest improvements"
```

### 8. Hook System
Control Claude's behavior with pre/post operation hooks:
- **Pre-hooks**: Validate before actions
- **Post-hooks**: Cleanup or notifications
- **Error hooks**: Handle failures gracefully

---

## Best Practices

### 1. Context Management
- **Use `/clear` frequently** between unrelated tasks to prevent context rot
- **Leverage `CLAUDE.md`** as shared memory (typically 1000-2000 tokens)
- **Spawn sub-agents** for complex problems to maintain focus
- **Maintain todo lists** for multi-step workflows

### 2. Prompt Engineering
- **Use XML tags** for structure: `<system-reminder>`, `<good-example>`, `<bad-example>`
- **Write explicit algorithms** with decision points
- **Include platform context**: OS, working directory, recent commits
- **"IMPORTANT/NEVER/ALWAYS"** still most effective for critical instructions
- **Provide contrasting examples** to clarify preferences

### 3. Tool Design
- **Match tool granularity to frequency**: High-frequency operations deserve dedicated tools
- **Provide fallbacks**: Bash for edge cases not covered by specialized tools
- **Include extensive examples** in tool descriptions
- **Optimize for debuggability** over cleverness

### 4. Security
- Never commit API keys
- Use read-only MCP servers for production
- Review permissions carefully
- Isolate in containers when using `--dangerously-skip-permissions`
- Enable manual approval for tool calls when using MCP with untrusted data

### 5. Performance Optimization
- **Use Haiku aggressively** (70-80% cost savings):
  - File reading and parsing
  - Web content processing
  - Git history analysis
  - UI label generation
- **Reserve Opus for**:
  - Initial planning and architecture
  - Complex multi-step reasoning
  - Critical decision points
- Implement token limits for MCP outputs (default: 25,000)
- Break large tasks into smaller chunks

### 6. Workflow Patterns

**Test-Driven Development:**
1. Write tests with explicit "no mock implementation" instruction
2. Verify tests fail
3. Implement code to pass tests
4. Use sub-agents to verify implementation isn't overfitting
5. Iterate until all pass

**Feature Implementation (The Claude Code Way):**
1. **Planning Phase** (Opus): Research and understand requirements
2. **Todo Creation**: Build explicit task list
3. **Implementation** (Sonnet): Execute with multi-file changes
4. **Verification** (Haiku): Read and validate changes
5. **Documentation**: Update README and create PR

**Debugging Pattern:**
1. Describe issue or paste error
2. Use ripgrep/find for codebase analysis (not RAG)
3. Spawn sub-agent for focused investigation if needed
4. Implement fix with Edit tool
5. Verify with smaller model

**The "Claude.md Pattern":**
```markdown
# Project Context
Tech stack: Next.js 15, TypeScript, Tailwind
Database: PostgreSQL with Prisma

# Preferences
- NEVER use the pages directory
- ALWAYS use app router
- Prefer server components over client components
- Use pnpm, not npm or yarn

# Known Issues
- Authentication flow requires specific header order
- Database migrations must run in transaction

# Architecture Decisions
- Feature-based folder structure
- Centralized error handling via middleware
```

---

## Next Steps

1. **Practice Basic Usage**: Start with simple file operations and code generation
2. **Explore MCP Servers**: Try integrating one external tool
3. **Create Custom Commands**: Build reusable workflows for your team
4. **Experiment with Sub-Agents**: Design specialized assistants
5. **Build Custom MCP Server**: Create integration for your specific tools

## Resources

### Official Documentation
- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code/overview)
- [MCP Protocol Specification](https://modelcontextprotocol.io)
- [Best Practices Guide](https://www.anthropic.com/engineering/claude-code-best-practices)

### Community Analysis & Reverse Engineering
- [MinusX Blog - Internal Architecture Analysis](https://minusx.ai/blog/decoding-claude-code/)
- [Yuyz0112 - Runtime Behavior Analysis](https://github.com/Yuyz0112/claude-code-reverse) 
  - Contains extracted prompts, tools, and workflow analysis
  - Interactive visualization tool for understanding API interactions
- [Community Examples](https://github.com/topics/claude-code-agents)

### Implementation References
- [Claude Code GitHub Actions](https://github.com/anthropics/claude-code-action)
- [Awesome Claude Code Commands](https://github.com/hesreallyhim/awesome-claude-code)
- [Claude Code Workflows](https://github.com/OneRedOak/claude-code-workflows)

---

## Summary

Claude Code represents a paradigm shift in AI-assisted development through its **commitment to simplicity**. Rather than complex multi-agent orchestration or opaque RAG systems, it succeeds through:

### Core Principles That Make Claude Code Effective:

1. **Architectural Simplicity**: One main loop, one branch maximum, one message history
2. **Transparent Search**: LLM-powered ripgrep/find commands you can inspect and debug
3. **Smart Model Usage**: 50%+ of calls to cheaper Haiku model, Opus only for planning
4. **Tool Hierarchy**: Mixed granularity tools that match usage frequency
5. **Self-Managed State**: Agent maintains its own todo list for focus and adaptability

### Key Technical Insights:

- **Token Distribution**: ~2,800 system prompt + ~9,400 tool descriptions + ~1,500 CLAUDE.md
- **Control Flow**: Flat message history with optional single-branch sub-agent spawning
- **Search Philosophy**: "LLM search is the camera vs. lidar of the AI era"
- **Steering Method**: XML tags, markdown structure, and yes, "IMPORTANT" still works best

### The "Bitter Lesson" for AI Agents:

Just as in machine learning broadly, **simpler architectures that leverage model improvements** outperform complex hand-engineered systems. Claude Code's success comes from:
- Building a good harness for the model
- Letting the model do the heavy lifting
- Avoiding excessive scaffolding that becomes technical debt
- Maintaining debuggability and transparency

Success with Claude Code—and AI agents generally—comes from understanding this philosophy: **Keep Things Simple, let the model shine in its wheelhouse, and resist the urge to over-engineer**. Start simple, experiment often, and gradually incorporate more advanced features only when they provide clear value.