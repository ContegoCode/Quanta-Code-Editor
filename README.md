<div align="center">

<img src="Frontend_UI/Graphics/4e59d123-a9b2-432f-8ca9-b73fa3b39b2e.png" alt="Quanta AI" />

# Quanta AI Code Editor

### The AI editor that runs entirely on your machine.

**No API keys. No subscription. No cloud. Your code never leaves your PC.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6.svg)]()
[![Built with Rust](https://img.shields.io/badge/Backend-Rust-orange.svg)](https://www.rust-lang.org/)
[![Tauri 2](https://img.shields.io/badge/Shell-Tauri%202-24C8DB.svg)](https://v2.tauri.app)
[![Powered by llama.cpp](https://img.shields.io/badge/Inference-llama.cpp-red.svg)](https://github.com/ggml-org/llama.cpp)
[![Architecture](https://img.shields.io/badge/Crates-21-blueviolet.svg)]()

[![Support on Ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/quantaaieditor)

</div>

---

<div align="center">

### Watch Quanta in Action

[![Watch the demo](https://img.youtube.com/vi/YwJawdqkH7k/hqdefault.jpg)](https://www.youtube.com/watch?v=YwJawdqkH7k)

*The agent reads your code, runs real tools, lands a diff — and one click rewinds the whole turn.*

</div>

---

## Why Quanta?

Every other AI editor makes the same trade: your code goes to their cloud, you pay their subscription, and the model acts on whatever stale snapshot it happened to read.

**Quanta was built the other way around.**

- 🔒 **Fully local** — the agent, the tools, and the model run on your machine via a managed `llama-server` (llama.cpp). Works on a plane. Works with your internet off.
- 🧊 **Nothing the model sees is stale** — this is the architecture's entire pillar. Canonical document state lives in Rust with version-gated writes, diagnostics are verified current at read time, and the repomap is re-validated before it reaches the context.
- ↩️ **Real undo for AI work** — every agent turn lands on a shadow-git checkpoint *before* it touches anything. Rewind a file, a hunk, or the whole turn — terminal side effects included.
- 🐞 **A real debugger** — Debug Adapter Protocol with codelldb, debugpy, and js-debug. The agent doesn't guess what your code did; it sets a breakpoint and *looks*.
- 🎨 **A media suite no other editor has** — images, video, music, and speech (with voice cloning) generated locally, in the same app that writes your code.
- 🧩 **Claude-Code-compatible plugins** — skills, agents, hooks, and MCP servers install from a plugin manifest, atomically, with rollback.
- 🆓 **Free and open source** — MIT licensed. Optional supporter tiers fund development; the editor is free forever.

---

## Built From the Ground Up — Not a Fork, Not AI Slop

v2 isn't v1 with patches, and it isn't a VS Code fork with an AI sidebar bolted on. **Every layer was redesigned and rewritten for this architecture** — a custom UI (React 19 + CodeMirror 6), a custom agent loop in Rust, a canonical document store, a managed model runtime — using only proven, deliberate methods:

- **No Electron. No VS Code OSS. No extension host.** The coupling every other "VS Code with AI" product inherits is gone — replaced by a purpose-built Tauri 2 shell that's faster, smaller, and owns exactly what it needs.
- **Rust owns the truth** — the agent loop, document state, LSP lifecycle, debugger, sessions, and checkpoints are all backend-authoritative. The UI renders state; it never *is* the state.
- **1,300+ tests** across the workspace — regression tests for the failure modes that actually hurt people (stale reads, lost edits, partial writes, session corruption)
- **Deterministic where it matters** — the repomap is a deterministic PageRank port, not "ask the model where things are." Freshness is verified by content hashes and version gates, not assumed.
- **Honest by design** — dependency-gated tools *hide* when their runtime is missing instead of failing mid-turn; `debug_state` reports what the adapter said, never a guess; off-recipe media settings are labeled, not silently applied.
- **Security that was added, not bolted on** — Ed25519-signed donor tokens (v1 skipped signature verification), memory threat-scanning on write *and* load, credential-path protection that no permission mode bypasses.
- **Meaningful engineering, not vibes** — VRAM gating is *measured* against `nvidia-smi`, not estimated. Checkpoint pruning physically reclaims disk. Compaction has a tier that cannot fail on model grounds.

This is what "not AI slop" looks like in practice: every claim in this README traces to code, every safety property has a test, and the parts that can't be verified are labeled honestly instead of hidden.

---

## Table of Contents

- [Built From the Ground Up](#built-from-the-ground-up--not-a-fork-not-ai-slop)
- [Quick Start](#quick-start)
- [The Agent](#the-agent)
- [Freshness — the Core Guarantee](#freshness--the-core-guarantee)
- [Safety — Defense in Depth](#safety--defense-in-depth)
- [Checkpoints & Undo](#checkpoints--undo)
- [Debugging (DAP)](#debugging-dap)
- [Local Media Suite](#local-media-suite)
- [Agent Tools](#agent-tools)
- [Code Intelligence](#code-intelligence)
- [Models & Vision](#models--vision)
- [Context & Compaction](#context--compaction)
- [Memory, Skills & Learning](#memory-skills--learning)
- [Plugins & Extensions](#plugins--extensions)
- [MCP Support](#mcp-support)
- [Sessions & Sharing](#sessions--sharing)
- [Terminal & Runtime](#terminal--runtime)
- [Accounts & Supporter Tiers](#accounts--supporter-tiers)
- [Architecture](#architecture)
- [Feature Comparison](#feature-comparison)
- [Configuration](#configuration)
- [License](#license)

---

## Quick Start

1. **Download** `Quanta_2.0.0_x64_Setup.exe` from [Releases](../../releases)
2. **Install & launch** — first run sets up the icon theme and managed tools automatically
3. **Create your free Quanta account** — a quick registration gates the app (it's how sessions, donor status, and updates stay in sync)
4. **Pick a model** — the built-in HuggingFace browser downloads GGUF weights directly; Quanta manages `llama-server` for you (no Ollama, no external installs)
5. **Open a folder, ask the agent to do something real** — *"find why this test flakes and fix it"*

That's it. Free account, no API keys, no cloud.

> **Vision-capable?** Drop a matching `mmproj` GGUF next to your model — Quanta auto-detects it. Attach screenshots of errors, mockups, or diagrams in chat and a vision model reads them directly.

---

## The Agent

Rust owns the loop. The UI is a window into it — not the driver.

- **Streaming tool calls** — watch the agent think, call tools, and render diffs live as it generates. No spinner-then-dump.
- **Review before apply** — every edit lands as a red/green diff card; accept or reject per-file and per-hunk
- **Three modes** — **Code** (full toolset), **Ask** (read-only research), **Plan** (read-only + plan file)
- **Sub-agents** — `spawn_agent` delegates scoped work to nested agents with their own context and tool scope
- **Teacher escalation** — a failed turn can escalate to a stronger configured model instead of just giving up
- **Repetition guard** — detects pathological verbatim loops in truncated responses
- **Per-turn registries** — tool sets rebuild each turn, so a skill or MCP server installed mid-session is usable immediately, no restart
- **Dependency-gated tools** — `DEP_GATED_TOOL_NAMES` hides tools whose runtime isn't there (no `sd-cli`, no `image_generate` offered). Missing prerequisites never become runtime failures.

## Freshness — the Core Guarantee

v2 was rebuilt around one problem: every AI editor's model eventually acts on stale state. Quanta's answer is architectural, not a lint check.

- **Canonical documents** — `doc_manager` is the single owner of file content, with three author-tagged write sources (Zed's `ChangeAuthor` pattern): `User` (editor keystrokes), `Agent` (write/edit/diff tools), `External` (filesystem watcher). Every mutation bumps a version and pushes a ring snapshot.
- **Precise dirty tracking** — `saved_version` vs `version` is exact; no hash-guessing whether a buffer is dirty
- **Version-gated edits** — the agent can't write to a file that changed underneath it
- **Content-hash index** — `code_index` stores the sha256 of indexed text and re-hashes the *current* canonical text before a result reaches the model. Unsaved buffers can't serve stale hits; an mtime bump without a real change doesn't re-index.
- **Verified repomap** — Aider's PageRank repo map, content-hash-cached, re-validated before it's injected into context
- **Fresh config** — `config.json` is read per call, never cached in a `RwLock`. A settings toggle takes effect on the very next tool call.
- **Snapshot-preserved prompt** — the system prompt freezes at session start so mid-session memory writes don't bust the prefix cache; volatile context (cursor, selection, terminal) is kept *out* of the prompt and into the user message for the same reason

This is the difference between an editor that *usually* knows what your code looks like and one that *verifiably* does.

## Safety — Defense in Depth

Safety isn't one check — it's five independent layers, any of which can stop a bad action:

| Layer | What it does |
|-------|--------------|
| **Mode allowlists** | `ASK_MODE_TOOLS` / `PLAN_MODE_TOOLS` / `CODE_MODE_EXCLUDED_TOOLS` — Ask can't write, Plan can only write plans |
| **Permission rules** | persistent `allow`/`ask`/`deny` lists in `~/.quanta/permissions.json`, with `Tool(pattern)` matching |
| **Command safety** | three-tier classifier (`Blocked` / `Dangerous` / `Safe`) — catastrophic commands are hard-denied, not just asked about |
| **File protection** | credential, VCS, and key-material paths require approval *regardless* of permission mode — the floor nothing bypasses |
| **Approval store** | pre-execution approval for destructive tools (`delete_path`, …) — the loop async-awaits your answer via oneshot channel |

And on the OS side: the **terminal sandbox** applies Restricted Token + DACL + Job Object on Windows (Codex `windows-sandbox-rs` derived) — child processes run caged, and `Job Object` cleanup guarantees nothing outlives its parent.

## Checkpoints & Undo

Every agent turn writes a shadow-git checkpoint **before** touching anything.

- Rewind a single edit — or the entire turn, including terminal side effects
- Browse the checkpoint timeline; diff any point against now
- Stored in `~/.quanta/checkpoints/` — never touches your project's `.git`
- Deleted and orphaned checkpoints are physically pruned — no silent disk growth

## Debugging (DAP)

A real Debug Adapter Protocol client — shaped after Zed's `dap` crate:

- **Adapters:** codelldb, debugpy, js-debug — resolved from `~/.quanta/dap/`
- **Framed transport** — stdio, or TCP for CodeLLDB with a client-allocated `--port`
- **Canonical state** — `DebugStore` owns breakpoints; `debug_state` reports what the adapter actually said, never a guess
- **Agent-visible** — the `debug` tool lets the model set breakpoints, run, and inspect — it debugs, not just edits

## Local Media Suite

All offline, all driven from chat — and all sharing one `sd-cli.exe` runtime:

| Tool | Engine | What it does |
|------|--------|--------------|
| `image_generate` | stable-diffusion.cpp (Qwen-Image 2.1 GGUF) | Text-to-image and image edits, fully local |
| `video_generate` | stable-diffusion.cpp (Wan 2.x) | Text-to-video and image-to-video — duration, quality tiers, LoRA lightning pairs, per-family recipes |
| `music_generate` | acestep.cpp (ACE-Step) | Text-to-music — vocals included |
| `speech_generate` | Qwen3-TTS (`llama-tts`) | Speech synthesis with **voice cloning** from a clip |
| `transcribe_audio` | Whisper | Speech-to-text for dictation |
| `view_image` / `inspect_video` | mtmd via `mmproj` | The agent can *see* images and video frames |
| `render_diagram` | — | Architecture and flow diagrams from text |

The video pipeline is the deep one — Wan family detection from GGUF architecture, VRAM-measured preflight (it can evict the chat model to make room, then restore it after), prompt enrichment by the chat model for terse inputs, multi-chunk phase prompts, and quality tiers tuned per model family. The model picker surfaces curated kit catalogs with `fitsNow`/`fitsCapacity` VRAM badges — it tells you what your GPU can run *before* you download 20 GB.

## Agent Tools

**40+ built-in tools**, streamed live while the model generates them:

| Group | Tools |
|-------|-------|
| Filesystem | `read_file` `write_file` `edit_file` `apply_diff` `list_directory` `find_path` `grep` `copy_path` `move_path` `create_directory` `delete_path` |
| Code intelligence | `lsp` (go-to-def, references, rename, code actions) · `diagnostics` · `semantic_search` · `read_document` |
| Execution | `terminal` (foreground + `background_jobs`) · `debug` (DAP) |
| Web | `fetch` · `web_search` · `image_search` |
| Media | `image_generate` `video_generate` `music_generate` `speech_generate` `transcribe_audio` `view_image` `inspect_video` `render_diagram` |
| Agent plumbing | `spawn_agent` `check_subagent` `todo_list` `suggest_tasks` `undo_edit` `memory` `skill` `search_sessions` `tool_search` `write_plan_file` `attempt_completion` |
| Ingest | `skill_ingest` `skill_corpus` `skill_finalize` `skill_check_fresh` `skill_refresh` — turn a repo, doc, or site into a usable skill |

Plus `tool_search` — on-demand discovery that defers MCP tool loading until the model actually needs it.

## Code Intelligence

- **LSP client** — synchronous stdio JSON-RPC client; language servers spawned as managed children with `installer` for auto-download
- **Repomap** — faithful port of Aider's `repomap.py`: tree-sitter symbols → file-reference graph → personalized PageRank → signature-aware rendering fit to a token budget. Deterministic — no model involved.
- **Semantic index** — embedding-backed `semantic_search` with the content-hash freshness guarantee
- **Diagnostics** — LSP errors/warnings with freshness tracking, fed straight to the model
- **Document reading** — `read_document` pulls text out of PDFs and docs the model couldn't otherwise see

## Models & Vision

- **llama.cpp only** — `llama-server.exe` is the sole local backend; GGUF models with automatic `mmproj` projector detection for vision
- **Single wire path** — every backend speaks OpenAI-compatible `/v1/chat/completions`; remote OpenAI-compatible endpoints are supported when you *choose* them — local is the default, not the fallback
- **HuggingFace browser** — search, file listing, resumable GGUF downloads, and a fit heuristic that warns when a model won't fit your hardware
- **Managed lifecycle** — Quanta spawns, health-checks, pauses, and resumes `llama-server` itself; it can even evict the chat model mid-run to free VRAM for video generation, then put it back
- **Per-model overrides** — temperature, thinking, context size, tool-call mode via `~/.quanta/model_overrides.json`
- **Token tracking** — real usage counts drive the compaction trigger, not guesses

## Context & Compaction

Three tiers, each able to bail out the next:

1. **Microcompaction** — stubs old tool results first; if that alone frees enough, the LLM is never invoked
2. **LLM summary** — a handoff prompt produces a compaction marker summarizing the history
3. **Deterministic floor** — archives messages behind a truncation marker until the wire fits. This tier *cannot* fail on model grounds — it's the guarantee under the other two.

Trigger: ~90% of the input budget (`num_ctx` minus a scaled output reserve), measured from actual token counts.

## Memory, Skills & Learning

- **Persistent memory** — `MEMORY.md`/`USER.md` under `~/.quanta/memories/`; the `memory` tool manages entries; writes are **threat-scanned** for injection and exfiltration patterns on write *and* on snapshot load
- **Context fencing** — memory is wrapped in a fenced block with a system note; a streaming scrubber strips anything that shouldn't reach the wire
- **Post-turn consolidation** — every `nudge_interval` turns, an LLM review consolidates memory in the background
- **Skills** — reusable instruction packs; project, global, built-in, or authored by the agent itself via `/learn`
- **Ingest pipeline** — `detect → acquire → audit → manifest` turns a doc, repo, or site into a skill with provenance and freshness stamps
- **Skill curator** — background lifecycle manager: archives stale agent-created skills on a schedule, snapshots before every run, writes per-run reports
- **Learning graph** — memories, skills, and sessions rendered as a connected graph in the UI

## Plugins & Extensions

**Claude-Code-compatible plugin format** — a `plugin.json` manifest plus `skills/`, `agents/`, `hooks.json`, `mcp.json`:

- **Atomic install** — all destinations preflighted for collisions; any mid-install failure rolls back every copied file
- **Component tracking** — every installed component is origin-tagged (`built-in` / `global` / `project` / `plugin:<name>`), so uninstall removes exactly what the plugin contributed and nothing else
- **Hooks** — shell commands wired to lifecycle events (`PreToolUse`, `PostToolUse`, `SessionStart`, `UserPromptSubmit`, `PreCompact`, `Stop`) with `{{placeholder}}` substitution and add-time rejection of dangerous commands
- **Marketplace** — browse and install from plugin sources
- **Skill linting** — installed skills are validated before the agent can load them

**Extensions** via an Open VSX-compatible mirror — v2 can't run arbitrary VS Code extensions (no extension host, by design), so it takes what it *can* honestly use: VSIX asset packs — themes, icon themes, snippets, grammars — extracted and classified under `~/.quanta/extensions/`. Host-required contributions are identified as such rather than silently broken.

## MCP Support

Full [Model Context Protocol](https://modelcontextprotocol.io) — a dedicated crate, not an afterthought:

- **Both transports** — stdio and streamable-HTTP JSON-RPC clients
- **Presets + custom** — built-in server presets plus your own `mcp_servers.json` entries
- **Live management** — connect/add/remove/enable/reconnect with deterministic ordering and status polling
- **Tool-cache refresh** — `tools/list_changed` notifications reload the exposed tool set automatically
- **`tool_search`** — MCP tools stay out of the context window until the model requests them by keyword
- **Managed Node** — `node_install` drops a pinned LTS runtime into `~/.quanta/node/` so `npx`-based servers work even on machines with no system Node at all

## Sessions & Sharing

- **SQLite-authoritative** — every session is durable, crash-safe, and searchable; v1's JSON dual-write is gone
- **FTS5 search** — `search_sessions` does full-text search across every past conversation
- **Thinking preserved** — `Message.thinking` stores reasoning text so thinking models don't degrade across tool turns
- **Export** — sessions export to portable formats
- **Shared sessions** — Supabase-backed live sharing: a host broadcasts a session, viewers follow along (writes through the Worker, reads direct via PostgREST with rate-limit-aware polling)

## Terminal & Runtime

- **PTY terminal** — `portable-pty` ConPTY hosts real shells; `xterm.js` renders them in the UI — bounded queue backpressure, coalesced output events, guaranteed cleanup on exit
- **Managed sidecars** — `llama-server`, `sd-cli`, ACE-Step, Qwen3-TTS, MinGit, Node — all downloaded on demand into `~/.quanta/`, never polluting the system
- **Windows sandbox** — Restricted Token + DACL + Job Object for spawned commands

## Accounts & Supporter Tiers

A free Quanta account is **required** to use the editor — registration keeps sessions, donor status, and update delivery in sync. Donations stay entirely optional.

- Donations via [Ko-fi](https://ko-fi.com/quantaaieditor) unlock cosmetic + convenience perks — custom display name, donor wall placement, early features
- **Supporter Wall** is public — anyone can see who's backing the project
- Tiers: Supporter → Bronze → Silver → Gold, verified by **Ed25519-signed** donor tokens (v1 never verified signatures; v2 does)
- Machine-bound, offline-capable gating — the token verifies locally, no phoning home

---

## Architecture

```
┌────────────────────────────────────────────────────────────────┐
│                         Quanta v2                              │
│                                                                │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │   Frontend — Tauri 2, React 19, CodeMirror 6, xterm.js   │  │
│  │   chat · diffs · checkpoints · model picker · media      │  │
│  │   learning graph · extensions · shared sessions          │  │
│  └──────────────────────────┬───────────────────────────────┘  │
│                             │ Tauri IPC (typed commands)       │
│  ┌──────────────────────────┴───────────────────────────────┐  │
│  │                Backend — Rust workspace (21 crates)      │  │
│  │                                                          │  │
│  │  agent ───── the ReAct loop, escalation, repetition      │  │
│  │  tools ────── 40+ tools, permissions, approvals, sandbox │  │
│  │  doc_manager ─ canonical documents (3-author versions)   │  │
│  │  lsp_client ─ language server lifecycle + sync           │  │
│  │  dap ──────── Debug Adapter Protocol (real debugging)    │  │
│  │  repomap ──── Aider-parity PageRank repo map             │  │
│  │  code_index ─ semantic search, content-hash freshness    │  │
│  │  session ──── SQLite + FTS5, export, shared sessions     │  │
│  │  checkpoints ─ shadow-git snapshots + rewind             │  │
│  │  memory ───── persistent memory, threat scan, fencing    │  │
│  │  plugins ──── skills/agents/hooks/MCP, ingest pipeline   │  │
│  │  mcp ──────── stdio + HTTP MCP clients                   │  │
│  │  curator ──── skill lifecycle, snapshots, reports        │  │
│  │  llm ──────── llama-server lifecycle, HF, overrides      │  │
│  │  prompt ───── cached system prompt, volatile context     │  │
│  │  platform ─── PTY, Job Objects, Windows sandbox          │  │
│  │                                                          │  │
│  │  ┌────────────┐ ┌────────────┐ ┌───────────────────────┐ │  │
│  │  │llama-server│ │ sd-cli.exe │ │ ace-step / TTS sidecars│ │  │
│  │  │ (llama.cpp)│ │ (sd.cpp)   │ │ managed on demand      │ │  │
│  │  └────────────┘ └────────────┘ └───────────────────────┘ │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

**What this buys you:**

- The agent loop, document truth, LSP state, debugger, and sessions are all **Rust-authoritative** — the UI renders state, it doesn't own it
- No Electron, no VS Code fork, no extension host — purpose-built Tauri shell means fast startup and a small installer
- Every sidecar is managed — downloaded on demand into `~/.quanta/`, version-pinned, never touching your system PATH

---

## Feature Comparison

| Capability | Quanta | Cursor | Zed | Copilot |
|---|:---:|:---:|:---:|:---:|
| Runs fully offline | ✅ | ❌ | ❌ | ❌ |
| No subscription required | ✅ | ❌ | Partial | ❌ |
| Local model as the default | ✅ llama.cpp | ❌ | ❌ | ❌ |
| Your code never leaves the PC | ✅ | ❌ | ❌ | ❌ |
| Stale-state protection (version-gated) | ✅ | ❌ | ❌ | ❌ |
| Rewind an entire agent turn | ✅ | Partial | ❌ | ❌ |
| Real debugger (DAP) | ✅ | ✅ | ✅ | ❌ |
| Local image/video/music/voice gen | ✅ | ❌ | ❌ | ❌ |
| Claude-Code-compatible plugins | ✅ | ❌ | ❌ | ❌ |
| Persistent memory + threat scanning | ✅ | ❌ | ❌ | ❌ |
| Skill ingest pipeline (doc/repo→skill) | ✅ | ❌ | ❌ | ❌ |
| MCP servers (stdio + HTTP) | ✅ | ✅ | ✅ | Partial |
| Price | **Free / donation** | $20/mo | Free + usage | $10/mo |

*Reflects publicly known behavior as of early 2026 — verify current specs if it matters to your decision.*

---

## Configuration

Everything lives under `~/.quanta/` — inspectable, portable, yours:

```
~/.quanta/
├── config.json          # app settings
├── account.json         # cached account profile
├── permissions.json     # allow/ask/deny rules
├── mcp_servers.json     # MCP server configs
├── model_overrides.json # per-model tuning
├── models/              # GGUF weights (chat + media)
├── checkpoints/         # shadow-git snapshots
├── sessions/            # SQLite session store
├── memories/            # MEMORY.md / USER.md
├── skills/              # authored + learned skills
├── plugins/             # installed plugin components
├── extensions/          # VSIX asset packs
├── node/                # managed Node LTS
├── dap/                 # debug adapters
├── ingest/              # skill corpus
├── images/ videos/ audio/   # generated media
├── voices/              # TTS voice clips
└── logs/                # curator reports, telemetry (off by default)
```

---

## License

MIT — see [LICENSE](LICENSE).

Copyright (c) 2026 Quanta AI

---

<div align="center">

**Quanta AI** — local-first. verifiably fresh. yours.

[Report Bug](../../issues) · [Request Feature](../../issues) · [Releases](../../releases) · [Ko-fi](https://ko-fi.com/quantaaieditor)

</div>
