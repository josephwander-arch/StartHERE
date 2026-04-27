# CPC — use the AI subscription you already pay for, on the computer you already own

Building something with AI? Most services today charge you per-credit to give an AI a terminal. CPC lets you skip the middle.

Your Claude, Codex, or Gemini subscription — the one you're already paying for. Your computer — the one that's already on. A set of small programs called **MCP servers** your AI already knows how to talk to. No servers of mine in the loop. I don't have any.

You install one. From then on, your AI can read your files, run commands, drive browsers, click Windows apps, and coordinate other coding agents — all on your machine. If your AI subscription is unlimited, CPC is unlimited. Usage counts against your plan the same as any other tool.

Built by one person. Apache 2.0 across the board. Permanently. No free tier that expires, no pricing page — there's no service to price.

---

## The pieces

| Repo | What it does | Tools |
|------|--------------|-------|
| **[local](https://github.com/AIWander/local)** | Shell, files, persistent sessions, breadcrumb-tracked multi-step ops | 105 |
| **[manager](https://github.com/AIWander/manager)** | Run many Claude Code / Codex / Gemini sessions in parallel, from chat | 48 |
| **[hands](https://github.com/AIWander/hands)** | Browser + Windows UI + vision automation in one binary | 116 |
| **[workflow](https://github.com/AIWander/workflow)** | Graduate browser automations into direct HTTP + encrypted credential vault | 37 |
| **[echo](https://github.com/AIWander/echo)** | Local AI via Ollama — cheap semantic search and summarization | 16 |
| **[cpc-breadcrumbs](https://github.com/AIWander/cpc-breadcrumbs)** | Shared crate — crash-safe operation tracking | library |
| **[cpc-paths](https://github.com/AIWander/cpc-paths)** | Shared crate — portable path discovery | library |
| **[voice](https://github.com/AIWander/voice)** | Talk to Claude Desktop - faster-whisper STT + edge-tts TTS | 10 |
| **[mcp-obsidian-bridge](https://github.com/AIWander/mcp-obsidian-bridge)** | Bridge local stdio MCP servers to claude.ai over HTTPS | utility |

Voice — talk to your AI while it works, it keeps running tools while you're thinking — is now live.

Once local is installed, say this to Claude in your chat window:

> `https://github.com/AIWander/voice` — Can you install this MCP for us to use here and the voice listening server, and make me a new `.bat` to call it and direct me to do what I need to do to get both sides running, then we can have a talk.

Same prompt works in Claude Code, Codex CLI, or Gemini CLI if you'd rather install it from a terminal agent. Full details in the [voice repo](https://github.com/AIWander/voice).

---

## Two-step install — local first, everything else for free

### Step 1 — Download and run the installer

One binary, double-click, done.

> **[Download install-local-v0.2-x64.exe](https://github.com/AIWander/local/releases/latest/download/install-local-v0.2-x64.exe)** · Windows x64 (Intel/AMD) · 9.1 MB
> SHA256: `799401d35285f84ac600d047bdcb4d434021a68cb6a00ec17c63985111c784eb`
>
> **[Download install-local-v0.2-arm64.exe](https://github.com/AIWander/local/releases/latest/download/install-local-v0.2-arm64.exe)** · Windows ARM64 (Surface/Qualcomm) · 7.95 MB
> SHA256: `2ee07b210b88c70423695adbc0b18cea675b3c391c512b2bb853b2291e86f685`

It drops `local.exe` and a matching `uninstall-local.exe` into `C:\CPC\servers\`, surgically edits your Claude Desktop config to register the `local` MCP server (your existing MCP servers are preserved, and the whole config is backed up first with a timestamped copy). Takes under 10 seconds. Quit Claude Desktop and reopen it — local is now live.

**Works on any Windows machine.** Google Drive doesn't need to be mounted at `C:\My Drive\Volumes`. Paths resolve through an environment variable with sensible fallbacks. Mount it wherever you want, or not at all.

**Clean uninstall whenever.** The installer drops `uninstall-local.exe` alongside `local.exe`. Standalone copies are also attached to each release if you ever need to re-grab one. No rogue files, no orphaned config entries.

On macOS? The manual path still works: grab the binary from the [latest release](https://github.com/AIWander/local/releases/latest), drop it in the right spot, add four lines to your Claude Desktop config, restart. That's the only manual install you'll ever do.

### Step 2 — Ask Claude to install the rest

In your normal chat window, say:

> *"Install hands, manager, and workflow from github.com/AIWander and wire them into my Claude Desktop config."*

Claude uses local's own tools to do it:

1. **Reads your Claude Desktop config** via local's file tools, so it knows where to write.
2. **Downloads the matching-arch binary** from each repo's latest release using local's HTTP client. ARM64 if you're on ARM, x64 if you're on x64 — it figures that out.
3. **Places each binary** in `C:\CPC\servers\` next to local.exe.
4. **Appends each server's entry** to `claude_desktop_config.json` — your existing entries stay put, your other MCP servers stay wired up.
5. **Tells you to quit and reopen Claude Desktop.**

You come back and have five MCP servers running. Self-bootstrapping from there — each server you add makes the next install easier because Claude gets more tools to help with it. Manager, for example, lets Claude delegate the next install to a background Claude Code session so you don't block the chat.

---

## What local does

The foundation piece. Think of it as your AI's hands and eyes on your Windows filesystem and shell. Over 100 tools, including:

- **Shell execution** — PowerShell, CMD, bash-in-WSL, plus persistent named sessions that keep env vars and working directory across calls. Run `cd C:\project; $env:DEBUG = 1; cargo build` as three separate tool calls in the same session and the state sticks.
- **File operations** — read, write, edit, line-range views, grep with context, diff between files, search by filename or content, binary-safe handling.
- **Transforms** — JSON pretty-print/minify, CSV ↔ JSON conversion, base64 encode/decode, SHA256/MD5 hashing, bulk rename with regex, find-and-replace across trees.
- **Git operations** — status, log, diff, commit, push, pull, branch, remote, stash, reset, clone — no shelling out to `git.exe`, proper error returns, structured output.
- **Breadcrumb tracking** — multi-step operations get automatic crash-safe state recording. Your AI can resume exactly where it stopped after a crash or a fresh chat.
- **Session management** — named persistent shell sessions with buffered output, command history, checkpoint/restore for long-running work.
- **HTTP client** — fetch, scrape, download to disk, full-method requests with custom headers and timing info.
- **Archive handling** — zip/unzip via PowerShell, no external dependencies.
- **Clipboard I/O** — read from and write to the Windows clipboard directly.
- **Config management** — back up and validate Claude Desktop config before any change. This is what the installer and uninstaller use under the hood.
- **SQLite queries** — read-only SELECT against any .db file. Useful for poking at indexes and local state.

And dozens more. See the full [tool list](https://github.com/AIWander/local) in the repo. Everything you'd want a shell agent to do without reaching for `cmd.exe`.

---

## Scale up with Manager

Five independent Claude Code sessions, each working on its own thing, all coordinated from your normal chat window — you watch them on a dashboard. Codex and Gemini CLI work today. Not coming soon. Today.

## Add Hands when you need a body

It reads web pages through the DOM, clicks Windows apps through accessibility, and when nothing else works, falls back to moving your mouse and reading your screen with OCR. It picks the right layer for the job instead of pixel-guessing.

## Add Workflow when a task repeats

Automate something through Hands once, Workflow captures the API calls underneath, and from then on your AI skips the browser and hits HTTP directly. Hundreds of times faster. Encrypted credential vault so tokens don't live in plaintext.

---

## See it running

![CPC dashboard](./assets/dashboard-preview.png)

This is Manager's dashboard, served on `localhost` the moment Manager is running. Sessions panel on top — every parallel Claude Code / Codex / Gemini job with live status. Today's scorecard below it. Active breadcrumbs on the bottom left — if something crashes, your AI picks up exactly where it stopped.

The dashboard is a single HTML file served directly by Manager. Nothing to install separately.

> **[Download dashboard preview HTML](https://github.com/AIWander/manager/releases/latest/download/dashboard.html)** — standalone version you can open in a browser to poke at the layout. The real one comes for free once Manager is running.

---

## How they fit

You chat with your AI in Claude Desktop. That's where the workflow starts. Everything else plugs in around that:

- **manager** delegates coding tasks out to Claude Code, Codex, or Gemini in their own sandboxes, so your main chat stays focused on deciding what to build rather than writing the code inline.
- **local** gives your AI read/write access to your files, a shell, and a breadcrumb system that picks up where you left off if something crashes.
- **hands** is the body — it drives browsers, clicks Windows apps, and reads what's on screen when nothing else works.
- **workflow** turns any repeated browser task into a direct HTTP call, with an encrypted credential vault so tokens never sit in plaintext.
- **echo** runs local Ollama models for cheap tasks that don't need to hit the cloud.

Each one stands alone. You don't need all of them. Pair them when a task crosses boundaries.

---

## Why this instead of agentic SaaS

Commercial agent platforms charge per action, run on their hardware, and keep your data on their servers. CPC runs on your machine, uses the AI subscription you already have, and stores everything locally. If you have unlimited AI use, you have unlimited CPC use. Usage rates are between you and your AI provider.

---

## What's intentionally private

CPC includes personal infrastructure — my own knowledge base, behavioral learning, cross-agent coordination state — that isn't part of the public release. The public repos are the reusable foundation.

---

## The honest version

I don't have a programming background. Every line of code in these repos was written by AI — Claude Code mostly, some Codex, some Gemini — coordinated by me through this same toolchain. My family pushed me to make something. Turns out if you build a tool that helps AI make better tools, the tools get better fast. That's how we got from zero to thirteen repos.

No company. No SaaS. No funding. No servers. Apache 2.0. Free forever.

File issues on any repo, or email [josephwander@gmail.com](mailto:josephwander@gmail.com). If something here saved you time, Cash App `$NeverRemember`.
