<div align="center">
 
### Watch Quanta AI in Action
 
[![Watch the video](https://img.youtube.com/vi/lVotfX5wLFA/maxresdefault.jpg)](https://www.youtube.com/shorts/lVotfX5wLFA)
 
</div>

# Quanta-Code-Editor
Quanta AI — Local-first AI coding agent
<div align="center">

# Quanta AI Code Editor

### A local-first, privacy-preserving AI code editor with a full agentic coding loop

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows-blue.svg)]()
[![Built with Rust](https://img.shields.io/badge/Built%20with-Rust-orange.svg)](https://www.rust-lang.org/)
[![Powered by Ollama](https://img.shields.io/badge/Powered%20by-Ollama-red.svg)](https://ollama.ai)

</div>

---

## What is Quanta?

Quanta is a **local-first AI code editor** built on VS Code OSS, powered by a high-performance Rust backend. It gives you a complete agentic coding experience — reading files, writing code, running terminals, applying LSP fixes, and managing git — all driven by local LLMs through [Ollama](https://ollama.ai). Cloud providers (OpenAI, Anthropic) are supported as optional backends, but Ollama is the primary engine. Your code never has to leave your machine.

Unlike cloud-first AI editors, Quanta is designed around local inference. The agent loop, tool execution, LSP integration, checkpoint system, and inline completions all happen locally through a Rust backend that communicates with the editor via JSON-RPC over TCP.

---

## Table of Contents

- [Key Features](#key-features)
- [Feature Comparison](#feature-comparison)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Agent Tools](#agent-tools)
- [Agent Modes](#agent-modes)
- [LSP & Diagnostics](#lsp--diagnostics)
- [Checkpoint System](#checkpoint-system)
- [Edit History & Undo/Redo](#edit-history--undoredo)
- [MCP Integration](#mcp-integration)
- [Model Management](#model-management)
- [HuggingFace Integration](#huggingface-integration)
- [Engineering Skills](#engineering-skills)
- [Voice & TTS](#voice--tts)
- [Session Management](#session-management)
- [Plan Mode](#plan-mode)
- [Configuration](#configuration)
- [License](#license)

---

## Key Features

### Core Agent
- **30+ built-in tools** — read/write/edit files, unified diffs, terminal, grep, glob, git operations, LSP actions, and more
- **ReAct agent loop** — Think, Act, Observe, Feedback pattern with anti-loop guards and automatic retries
- **3 agent modes** — Code (full capability), Ask (read-only), Plan (read-only + plan writing)
- **Sub-agent spawning** — Delegate scoped tasks to parallel sub-agents with up to 3 levels of nesting
- **Persistent todo lists** — Track multi-step work across conversation turns

### Local-First
- **Ollama integration** — Auto-detects and lists all local models with metadata
- **Thinking/reasoning support** — Configurable think levels (Low/Medium/High) for reasoning models
- **Inline code completion** — FIM completions with LRU cache, debouncing, and in-flight cancellation
- **Local-first by design** — Ollama is the primary backend; cloud providers (OpenAI, Anthropic) are optional. Your code never has to leave your machine.

### Safety & Control
- **Shadow-git checkpoints** — Automatic workspace snapshots before every agent write action
- **Edit review system** — Accept/reject individual edits with diff previews
- **Stale-file detection** — Prevents edits to files that changed since last read
- **Terminal safety guards** — Blocks destructive commands (format, shutdown, force-delete)
- **Atomic writes** — All file operations use temp-file-and-rename for crash safety

### Developer Experience
- **Full LSP integration** — Diagnostics, go-to-definition, find references, code actions, rename symbol
- **20+ engineering skills** — Built-in guidance for TDD, code review, security review, debugging, and more
- **MCP support** — One-click enable for GitHub, Jina AI, Brave Search, Postgres, Puppeteer, and more
- **HuggingFace model browser** — Search, download, and install GGUF models directly from the editor
- **Per-model configuration** — Override temperature, think level, edit format, tool call mode, and more per model
- **Voice support** — Speech-to-text via Whisper, text-to-speech via Piper

---

## Feature Comparison

> **Note:** Competitor data is based on publicly available documentation as of 2025. Features change frequently — verify with each tool's official docs before relying on this table for decisions. A dash (—) means we could not verify the feature's presence or absence and chose not to guess.

| Feature | Quanta | Cursor | Claude Code | Zed AI | Aider | Cline |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| **Local LLM (Ollama)** | Yes | Limited ¹ | Yes ² | Yes | Yes | Yes |
| **Ollama as primary backend** | Yes | No | No | No | No | No |
| **Built on VS Code** | Yes | Yes | No (CLI) | No (Zed) | No (CLI) | Yes (extension) |
| **Rust backend** | Yes | No | No | Yes | No | No |
| **Persistent shadow-git checkpoints** | Yes | No ³ | No | No | No | Yes |
| **Edit review (accept/reject)** | Yes | Yes | No | No | No | Yes |
| **LSP diagnostics to model** | Yes | Yes | No | Yes | No | Yes |
| **LSP code actions to model** | Yes | — | No | — | No | — |
| **LSP rename symbol to model** | Yes | — | No | — | No | — |
| **Inline completion (local)** | Yes | Yes | No | Yes | No | No |
| **Sub-agent spawning** | Yes | No ⁴ | Yes | No | No | No |
| **Plan mode** | Yes | Yes | No | No | No | Yes |
| **MCP support** | Yes | Yes | Yes | No | No | Yes |
| **HuggingFace model browser** | Yes | No | No | No | No | No |
| **Per-model config overrides** | Yes | No | No | Partial | No | No |
| **Per-task model routing** | Yes | Yes | Yes | Yes | No | No |
| **Engineering skills** | Yes | No | No | No | No | No |
| **Voice (STT + TTS)** | Yes | No | No | No | No | No |
| **Session export (MD/JSON/PDF)** | Yes | No | No | No | No | No |
| **Configurable thinking levels** | Yes | No | No | No | No | No |
| **Tool call modes (parallel/sequential)** | Yes | No | No | No | No | No |
| **Tree-sitter fallback diagnostics** | Yes | No | No | No | No | No |
| **Custom skills (project + global)** | Yes | No | No | No | No | No |
| **Todo list tracking** | Yes | No | Yes | No | No | Yes |

**Footnotes:**

1. **Cursor** — Supports Ollama via OpenAI-compatible endpoint for chat and tab completion, but agent modes do not work with local LLMs as of May 2025 (community feature request open).
2. **Claude Code** — Supports local Ollama models via Anthropic-compatible API endpoint (Ollama 0.14+). Cloud (Anthropic) is the default backend.
3. **Cursor** — Has session-local checkpoints that do not persist across IDE restarts and do not use a shadow git repository. They capture file changes only, not terminal side effects.
4. **Cursor** — Has parallel agents via git worktrees (Agents Window) and `/best-of-n` multi-model runs, but does not support spawning sub-agents from within an ongoing conversation.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Quanta AI Editor                        │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              VS Code OSS (Electron Frontend)          │  │
│  │  ┌───────────────┐  ┌─────────────┐  ┌──────────────┐ │  │
│  │  │  Chat Webview │  │   Editor    │  │  Inline Comp │ │  │
│  │  │  (TypeScript) │  │  (Monaco)   │  │  (TypeScript)│ │  │
│  │  └──────┬────────┘  └──────┬──────┘  └──────┬───────┘ │  │
│  │         │                  │                │         │  │
│  │  ┌──────┴──────────────────┴────────────────┴───────┐ │  │
│  │  │           Quanta Extension (TypeScript)          │ │  │
│  │  │     RPC Client · Diagnostics · LSP Bridge        │ │  │
│  │  └──────────────────────┬───────────────────────────┘ │  │
│  └─────────────────────────┼─────────────────────────────┘  │
│                            │ JSON-RPC 2.0 over TCP          │
│  ┌─────────────────────────┴─────────────────────────────┐  │
│  │              Quanta Backend (Rust)                    │  │
│  │  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐ │  
│  │  │ Agent Loop  │  │ Tool Registry│  │  Session Manager │ │ 
│  │  │ (ReAct)     │  │ (30+ tools)  │  │  (persistence)   │ │ 
│  │  └──────┬──────┘  └──────────────┘  └──────────────────┘ │ 
│  │  ┌──────┴──────────────────────────────────────────────┐ │ 
│  │  │  Checkpoint Service · MCP · LSP Reverse RPC         │ │ 
│  │  └─────────────────────────────────────────────────────┘ │ 
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────┐   │  │
│  │  │   Ollama    │  │  OpenAI API │  │ Anthropic API│   │  │
│  │  │ (localhost) │  │  (optional) │  │  (optional)  │   │  │
│  │  └─────────────┘  └─────────────┘  └──────────────┘   │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

**Key design principles:**
- The extension is a thin UI layer — all agent logic lives in the Rust backend
- Communication via newline-delimited JSON-RPC 2.0 over TCP (localhost only)
- The backend manages tool execution, LSP reverse-RPC, checkpoints, sessions, and MCP
- Cloud providers (OpenAI, Anthropic) are optional — Ollama is the primary inference engine

---

## Quick Start

### Prerequisites

1. **[Ollama](https://ollama.ai)** — Install and start the Ollama service
   ```cmd
   # Install a model (example)
   ollama pull qwen2.5-coder:7b
   ```

2. **Git** — Required for the checkpoint system

### Run Quanta

1. **Download** the latest release from the [Releases](../../releases) page
2. **Extract** the archive and run `Quanta.exe`
3. **Open a project** folder (File > Open Folder)
4. **Open the chat panel** — Click the Quanta icon in the activity bar
5. **Select a model** — Click the model name in the chat header to pick from your Ollama models
6. **Start coding** — Ask Quanta to build features, fix bugs, refactor code, or explain your codebase

> **Tip:** Use `@` in the chat input to mention files and inject them as context.

---

## Installation

### Option 1: Pre-built Release (Recommended)

Download the latest release from the [Releases](../../releases) page. Extract and run — no build tools required.

### Option 2: Build from Source

#### Prerequisites

- [Rust](https://rustup.rs) (stable, with cargo)
- [Node.js](https://nodejs.org) v20+
- [Ollama](https://ollama.ai) running on `localhost:11434`
- Git

---

## Agent Tools

Quanta's agent has access to **30+ tools** organized into functional groups:

### Filesystem & Terminal

| Tool | Description |
|------|-------------|
| `read_file` | Read file contents with line numbers (10MB limit, outline for large files) |
| `write_file` | Create or overwrite files atomically (auto-creates parent dirs) |
| `edit_file` | Find-and-replace edits with multi-strategy matching (exact, fuzzy, ellipsis) |
| `apply_diff` | Apply unified diffs with 7-strategy flexible patching |
| `list_directory` | List directory contents (dirs first, then files, alphabetical) |
| `find_path` | Glob-based file search (`**/*.rs`, respects .gitignore) |
| `grep` | Regex content search across files (with context lines, pagination) |
| `terminal` | Execute shell commands (safety guards, streaming output, sandbox support) |
| `create_directory` | Create directories recursively |
| `delete_path` | Delete files or directories (blocked in Code mode for safety) |
| `copy_path` | Copy files or directories recursively |
| `move_path` | Move or rename files (atomic when possible) |

### LSP Integration

| Tool | Description |
|------|-------------|
| `diagnostics` | Get LSP errors/warnings with freshness tracking and tree-sitter fallback |
| `go_to_definition` | Jump to symbol definition via reverse RPC to VS Code |
| `find_references` | Find all references to a symbol across the project |
| `get_code_actions` | Get available quick fixes and refactorings |
| `apply_code_action` | Apply a code action with edit tracking and staleness checks |
| `rename_symbol` | Rename a symbol across the entire workspace |

### Git

| Tool | Description |
|------|-------------|
| `git_status` | Show working tree status with branch info |
| `git_diff` | Show staged or unstaged changes |
| `git_commit` | Stage and commit changes (auto-creates .gitignore if missing) |
| `git_branch` | Create, switch, or list branches |
| `git_log` | Show recent commit history |
| `git_stash` | Stash, pop, or list stashes |

### Agent Management

| Tool | Description |
|------|-------------|
| `spawn_agent` | Spawn synchronous or async sub-agents (up to 3 levels deep) |
| `create_thread` | Create independent background conversation threads |
| `check_subagent` | Check status and retrieve results from async sub-agents |
| `list_agents_and_models` | List available agents and models |

### Network & Context

| Tool | Description |
|------|-------------|
| `fetch` | HTTP GET with HTML-to-Markdown conversion |
| `image_search` | Search the web for images (DuckDuckGo, no API key) |
| `skill` | Load engineering skills from project, global, or built-in sources |
| `tool_search` | On-demand deferred loading of MCP tools |
| `write_plan_file` | Write implementation plans in Plan mode |
| `undo_edit` | Undo the most recent accepted edit to a file |
| `todo_list` | Persistent task tracking across conversation turns |

---

## Agent Modes

| Mode | Capabilities | Use Case |
|------|-------------|----------|
| **Code** | Full toolset (except `delete_path`) | Building features, fixing bugs, refactoring |
| **Ask** | Read-only (no file writes, no terminal) | Understanding code, asking questions |
| **Plan** | Read-only + `write_plan_file` | Planning before implementing |

Switch modes using the mode button in the chat header.

---

## LSP & Diagnostics

Quanta provides deep LSP integration that feeds real-time diagnostics to the model:

- **Push-based diagnostics** — VS Code sends diagnostics to the backend cache as they change
- **Per-file version tracking** — Ensures diagnostics are fresh and correctly scoped
- **Deduplication** — Merges diagnostics from multiple sources (LSP, tree-sitter)
- **Auto-refresh** — Diagnostics are re-fetched after agent file edits
- **Tree-sitter fallback** — Syntax-based diagnostics for languages without an LSP
- **Language-specific configuration** — Auto-configures LSP servers for 20+ languages

**Supported LSP features:**
- Diagnostics (errors, warnings, info, hints)
- Go to definition
- Find references
- Code actions (quick fixes, refactorings)
- Apply code action (with edit tracking)
- Rename symbol (workspace-wide)

**Auto-configured languages:** C#, TypeScript/JavaScript, Rust, Python, Go, Java, Kotlin, Dart, Ruby, F#, Erlang, Haskell, D, R, LaTeX, and more.

---

## Checkpoint System

Quanta creates automatic **workspace-level snapshots** using a shadow git repository — completely separate from your project's own git repo.

- **Automatic** — A checkpoint is saved before every agent write action
- **Works without git** — Your project doesn't need to be a git repo
- **Workspace-level** — Covers edits, deletes, moves, and terminal-generated changes
- **Browseable** — View checkpoint timeline with messages and timestamps
- **Restorable** — Revert the entire workspace to any checkpoint
- **Diffable** — Compare any checkpoint against current state or another checkpoint
- **Deletable** — Select and delete individual checkpoints or all at once
- **Safe** — Checkpoints are stored in `~/.quanta/checkpoints/` and never touch your project

---

## Edit History & Undo/Redo

Every agent edit is tracked in a per-edit history with full undo/redo support:

- **Diff preview** — See exactly what changed before accepting
- **Accept/reject individually** — Review each edit one at a time
- **Accept/reject all** — Bulk operations for multi-file changes
- **Undo** — Revert specific edits after they've been applied
- **Redo** — Re-apply undone edits
- **Full diff view** — Open a complete diff in a separate editor panel
- **Edit history panel** — Timeline of all edits in the current session

---

## MCP Integration

Quanta supports the [Model Context Protocol](https://modelcontextprotocol.io) for extending the agent with external tools:

### Built-in MCP Servers (One-Click Enable)

| Server | Tools | Description |
|--------|-------|-------------|
| **Jina AI** | 20 | Web reading, search, screenshots, academic search (arXiv/SSRN), image search, reranking, classification, PDF extraction. Requires Jina API key. |
| **GitHub** | 7 | Search repositories, read files, create issues, list issues, create PRs, create branches, push files. Requires GitHub token. |
| **Filesystem** | 6 | Read, write, list, create, move, and search files on the local filesystem. No API key required. |
| **Fetch** | 1 | Fetch web pages and convert HTML to markdown. Requires uvx (Python). No API key required. |
| **Git** | 5 | Git status, diff, log, commit, and branch management. Requires uvx (Python). No API key required. |
| **Sequential-thinking** | 1 | Structured step-by-step reasoning with branching, revision, and dynamic thought count. Recommended for Plan mode. |
| **Memory** | 4 | Persistent knowledge graph — create entities, relations, search nodes, read graph. |
| **HuggingFace** | 4 | Search models, get model info, list model files, download models. Requires HF token. |
| **Serena** | 7 | Semantic code analysis via LSP — find symbols, references, get details, replace symbol bodies, insert code before/after. Requires uvx. |
| **Playwright** | 6 | Browser automation — navigate, click, fill, screenshot, evaluate JS, select options. Official Microsoft server. |
| **Arxiv** | 5 | Search arXiv papers (free-text, author, category), get full metadata, list subject categories. |
| **Augments** | 7 | Coding research — API docs, code examples, version comparisons, migration guides, error diagnosis, dependency scanning. Optional GitHub token. |
| **PlantUML** | 4 | Generate UML diagrams (sequence, class, activity, and more) from text descriptions. |
| **Context7** | 2 | Up-to-date library and framework documentation fetched live from the source. |

### MCP Features
- **On-demand tool loading** — MCP tools are deferred until needed, reducing context overhead by ~85%
- **Custom server configuration** — Add any MCP-compatible server
- **API key management** — Securely store credentials for MCP servers
- **Server status monitoring** — See connection status at a glance
- **Tool search** — The agent can search for and load MCP tools by keyword

---

## Model Management

### Per-Task Model Routing

Route different models to different tasks for optimal performance:

| Task | Config Setting | Description |
|------|---------------|-------------|
| Main chat | `quanta.defaultModel` | Primary model for agent conversations |
| Sub-agents | `quanta.subagentModel` | Model for spawned sub-agents |
| Summarization | `quanta.summarizationModel` | Model for auto-generating conversation titles |
| Inline completion | `quanta.inlineCompletion.model` | Coder model for FIM completions |

### Per-Model Configuration Overrides

Fine-tune each model individually via the Local Model Override Settings panel (`~/.quanta/model_overrides.json`):

| Setting | Options | Description |
|---------|---------|-------------|
| Think level | Low / Medium / High | Reasoning depth for thinking models |
| Temperature | 0.0–2.0 | Sampling temperature |
| Top-p | 0.0–1.0 | Nucleus sampling threshold |
| Top-k | 1–100 | Top-k sampling |
| Num predict multiplier | 1x–10x | Scale max output tokens |
| Tool call mode | Parallel / Sequential | How the model calls tools |
| Edit format | WholeFile / UnifiedDiff / FindReplace | Preferred file editing strategy |
| Context window | Custom | Override the model's context length |
| Prefer write_file | On / Off | Prefer whole-file writes (with fallback to edit/diff) |
| Preserve thinking | On / Off | Keep thinking content during context compaction |
| Max empty retries | 1–10 | Retries for empty completions (thinking models) |
| Max unfinished retries | 1–10 | Retries when LSP errors remain |

**Override priority:** User override > Built-in profile > Baseline defaults

---

## HuggingFace Integration

Browse and install models directly from HuggingFace without leaving the editor:

- **Search** — Find GGUF models by name, filter by pipeline tag and size
- **Sort** — By downloads, likes, or relevance
- **Download** — GGUF files or safetensors directories with progress tracking
- **Auto-register** — Downloaded models are automatically registered with Ollama
- **Hardware specs** — Detects CPU, RAM, GPU VRAM to help you pick the right model
- **Cleanup** — Tracks and removes orphaned blobs from cancelled downloads
- **Model management** — View and delete locally installed models

---

## Engineering Skills

Quanta includes **20+ built-in engineering skills** that provide structured guidance for common development tasks:

### User-Invoked Skills
| Skill | Description |
|-------|-------------|
| `help` | Get help with Quanta features and commands |
| `implement` | Implementation guidance for features |
| `init` | Initialize a new project with Quanta |
| `setup-project` | Configure project with issue tracker |
| `wayfinder` | Navigate and understand a codebase |
| `triage` | Triage and prioritize issues |
| `to-spec` | Convert requirements to specifications |
| `to-tickets` | Convert specifications to tickets |
| `grill-with-docs` | Validate code against documentation |
| `improve-codebase-architecture` | Systematic architecture improvement |

### Model-Invoked Skills
| Skill | Description |
|-------|-------------|
| `tdd` | Test-driven development with mocking |
| `code-review` | Structured code review |
| `security-review` | Security vulnerability review |
| `diagnosing-bugs` | Systematic bug diagnosis |
| `deep-research` | Deep research methodology |
| `research` | General research approach |
| `prototype` | Prototyping (logic + UI) |
| `domain-modeling` | Domain modeling with ADRs |
| `codebase-design` | Architecture design (Design-It-Twice) |
| `simplify` | Code simplification |
| `verify` | Verification strategies |
| `resolving-merge-conflicts` | Merge conflict resolution |
| `webapp-testing` | Web application testing |
| `grilling` | Code quality grilling |

### Custom Skills
Create your own skills in:
- **Project-local:** `.agents/skills/{name}/SKILL.md`
- **Global:** `~/.agents/skills/{name}/SKILL.md`

---

## Voice & TTS

### Speech-to-Text
- integration (tiny.en, base.en, small.en models)
- Configurable language
- Dictation toggle: `Ctrl+Shift+M`
- Audio buffer processing with temporary file handling

### Text-to-Speech
- integration with voice model downloads from HuggingFace
- Configurable voice (e.g., `en_US-lessac-medium`)
- Speed control
- Read aloud toggle: `Ctrl+Shift+L`
- Markdown cleaning for natural speech
- Voice listing (installed and available)

---

## Session Management

### Conversations
- Create, list, load, and delete sessions
- Auto-generated conversation titles
- Search sessions by content
- Per-session and cumulative token usage tracking
- Session persistence to disk
- Cascade delete for sub-agent sessions

### Export & Import
- **Export** to Markdown, JSON, or PDF
- **Import** from JSON or Markdown
- Exports include thinking content, tool calls, and timestamps

---

## Plan Mode

Plan before you build:

1. Switch to **Plan mode** in the chat header
2. Ask Quanta to create an implementation plan
3. Review the rendered markdown plan in the plan panel
4. Click **Implement Plan** to switch to Code mode and execute
5. Edit the plan at any time via **Edit Plan**

Plan files are saved to `.quanta/plans/` and persist across sessions.

---

## Configuration

### Server
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.serverHost` | `127.0.0.1` | Backend server host |
| `quanta.serverPort` | `8080` | Backend server port |
| `quanta.serverPath` | `""` | Custom path to backend binary |
| `quanta.autoStartServer` | `true` | Auto-start backend on activation |

### Models
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.defaultModel` | `""` | Default model (empty = first available) |
| `quanta.subagentModel` | `""` | Model for sub-agents |
| `quanta.summarizationModel` | `""` | Model for title generation |

### Inline Completion
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.inlineCompletion.enabled` | `true` | Enable inline completions |
| `quanta.inlineCompletion.model` | `""` | Override completion model |
| `quanta.inlineCompletion.debounceMs` | `300` | Debounce delay |
| `quanta.inlineCompletion.maxContextLines` | `100` | Context lines to send |
| `quanta.inlineCompletion.minPrefixChars` | `3` | Minimum prefix to trigger |

### Editing
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.autoApproveEdits` | `false` | Auto-approve without diff preview |
| `quanta.terminalSandbox` | `false` | Terminal sandbox mode |

### Diagnostics
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.treeSitterFallback` | `true` | Tree-sitter syntax linting fallback |
| `quanta.diagnosticsWaitMs` | `2000` | LSP diagnostic wait time |

### Voice
| Setting | Default | Description |
|---------|---------|-------------|
| `quanta.speechToText.enabled` | `false` | Enable speech-to-text |
| `quanta.speechToText.language` | `en` | Language code |
| `quanta.textToSpeech.enabled` | `false` | Enable text-to-speech 
| `quanta.textToSpeech.speed` | `1.0` | Speech speed |

---

## License

MIT License — see [LICENSE](Extension/LICENSE) for details.

Copyright (c) 2026 Quanta AI

---

<div align="center">

**Quanta AI** — Local-first AI coding, powered by Rust and Ollama.

[Report Bug](../../issues) · [Request Feature](../../issues) · [Releases](../../releases)

</div>
