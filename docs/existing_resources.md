Yep — you were right. There are **more**, and the category is already forming.

The big update: I found one that is basically your exact “Prompt in public” idea.

## The closest thing to your vision: YellingAtBots + whogitit

**YellingAtBots** is explicitly a public prompt-sharing layer for AI-built projects. Its homepage says “Prompts are the new source code” and lets people browse projects by the prompts and models that created them. It is powered by **whogitit**, and its flow is: use Claude Code or another AI coding tool, push to GitHub, then a GitHub Action syncs prompts into a public project timeline. That is very close to “new Twitter/X for open-source prompts.” ([Yelling At Bots][1])

Underneath that, **whogitit** tracks AI-generated code at line-level granularity, preserves the prompts that generated code, stores attribution in Git notes, integrates with Claude Code hooks, redacts sensitive data, and can produce PR attribution summaries with prompts. It even has `whogitit prompt src/main.rs:42` to show the exact prompt behind a line of code. ([GitHub][2])

That is the most direct hit I found.

## Public-profile / social-adjacent tools

**BlamePrompt** is also strongly aligned with the “prompts as public proof of skill” idea. Its README says: “Your AI skills deserve a portfolio,” and describes public profiles, prompt heatmaps, badges, leaderboards, AI-vs-human code ratios, and receipts attached to commits via Git notes. It says it supports 15 agents, including Claude Code and OpenAI Codex CLI. ([GitHub][3])

The VS Code extension for BlamePrompt says it shows prompt receipts grouped by commit/session, supports searching prompts, shows model/cost/prompt summaries above AI-generated blocks, and stores receipts as Git notes under `refs/notes/blameprompt`. ([Visual Studio Marketplace][4])

**Thinky Face** is not specifically git-history-for-code, but it is a public/private prompt library with version history, public profiles, team sharing, fork/share behavior, and an MCP server that lets Claude retrieve prompts. This is closer to “PromptHub/GitHub for prompts” than “prompt trail attached to commits.” ([thinkyface.com][5])

## Git-native provenance tools

**Git AI** is a serious, larger-scale player here. It is an open-source Git extension that tracks AI-generated code and links AI-written lines to the agent, model, and transcripts that generated them. It supports Claude Code, Codex, Cursor, Copilot, OpenCode, Windsurf, Gemini, Continue, and others. It uses Git notes for AI attribution and stores or points to transcripts separately, with team/cloud/self-hosted options. ([GitHub][6])

**Entire CLI** is another important one. It captures agent sessions, prompts, checkpoints, and file changes alongside code. Its docs/search results say it supports Claude Code and Codex via hook configs, and stores session/checkpoint data on an `entire/checkpoints/v1` branch. It also supports a separate checkpoint remote, which is relevant for public repos where you may want code public but prompt/session metadata private or separately controlled. ([GitHub][7])

**claude-prompt-trail** is very close to the “prompts belong in version control” formulation. It automatically captures Claude Code prompts and attaches them to Git commits using Git notes. It collects user prompts since the previous commit, attaches them under `refs/notes/claude-prompt-trail`, and can push those notes to origin. Its README explicitly argues that prompts shaping AI-generated code are as important as the code itself. ([GitHub][8])

**Prompt Recorder** is a Claude Code skill listed on MCP Market that records and syncs user prompts to Git notes and PR/MR descriptions. It says it attaches cleaned, actionable user prompts to commits and aggregates them into a “Prompt Documentation” section in GitHub PRs or GitLab MRs. ([MCP Market][9])

**git-prompt-story** is still relevant. It captures LLM sessions in Git history, making prompts reviewable, searchable, and part of a project’s permanent record. Its listed features include automatic capture, privacy scrubbing, review before push, and prompt tracking in commit messages. ([GitHub][10])

## MCP/local prompt-log tools

**Prompt Log MCP Server** is an actual MCP server that logs prompts from Claude Code and Claude Desktop for reflection/journaling. It stores locally by default in SQLite, supports search, tags, stats, and exports to JSON/CSV/Markdown. It is not a public posting product by itself, but it is a useful capture layer. ([GitHub][11])

**prompt-capture-mcp** captures every prompt sent from Claude Code to a local Markdown file using Claude Code’s `UserPromptSubmit` hook. It records project/workspace/session/model context and timestamps. Again, local-first, not public-first. ([GitHub][12])

**devctx** is an MCP server for persistent Claude Code context. It is not primarily a public prompt-publishing tool, but it logs project activity, sessions, todos, branch notes, and git events into `.devctx/`, with a dashboard and git-hook capture. It is adjacent because it treats agent work as durable repo context. ([GitHub][13])

**git-notes-memory** is archived, but worth noting: it stores Claude Code “memories” as Git notes with semantic search, includes secrets filtering, and has Claude Code hook integration including `UserPromptSubmit`. It is more “memory layer” than “prompt social network.” ([GitHub][14])

## Codex-specific local history/export tooling

There are also tools focused on reading Codex’s local session data rather than publishing it.

**codex-conversation-cli** inspects and exports Codex Desktop conversations from `~/.codex`, using session JSONL/event streams as the source of truth. ([GitHub][15])

**codex-history-list** scans `~/.codex/sessions`, extracts working directory and first user ask, and helps resume old sessions. ([GitHub][16])

**codex-sessions** is a TUI for browsing/searching/managing Codex CLI session logs and resuming sessions. ([GitHub][17])

These are not public posting tools, but they are the raw capture/export layer a “Prompt in Public” product could build on.

## My updated read

You are not imagining this. The market has split into three layers:

1. **Capture/provenance layer**: whogitit, Git AI, Entire, BlamePrompt, claude-prompt-trail, Prompt Recorder.
2. **Local memory/log layer**: Prompt Log MCP, prompt-capture-mcp, devctx, git-notes-memory, Codex session tools.
3. **Public/social layer**: YellingAtBots, BlamePrompt profiles, Thinky Face public profiles.

The closest direct answer is:

> **Yes: YellingAtBots + whogitit is already trying to be “Prompt in public” for AI-built projects. BlamePrompt is trying to make prompt history into a public developer profile. Git AI / Entire / whogitit / claude-prompt-trail are building the Git-native substrate.**

Your sharper version still has room: **open-source-only, opt-in, GitHub-native, commit/PR-attached prompt timelines with strong redaction and human curation.** The tools I found mostly solve capture/provenance; only YellingAtBots really leans into the public browsing/social product.

[1]: https://yellingatbots.com/ "yellingatbots - prompts are the new source code"
[2]: https://github.com/dotsetlabs/whogitit "GitHub - dotsetlabs/whogitit: Track AI-generated code at line-level granularity. Know which lines were written by AI, modified by humans, and what prompts generated them. Git-native storage, automatic redaction, and Claude Code integration. · GitHub"
[3]: https://github.com/Ekaanth/blameprompt "GitHub - Ekaanth/blameprompt: Git blame for prompts — Track AI-generated code provenance. No API key required. · GitHub"
[4]: https://marketplace.visualstudio.com/items?itemName=Blameprompt.blameprompt "
        BlamePrompt - Visual Studio Marketplace
    "
[5]: https://thinkyface.com/ "Thinky Face - The prompt library for AI power users | Thinky Face"
[6]: https://github.com/git-ai-project/git-ai "GitHub - git-ai-project/git-ai: A Git extension for tracking the AI-generated code in your repos · GitHub"
[7]: https://github.com/entireio/cli?utm_source=chatgpt.com "entireio/cli: 📜 Entire CLI hooks into ..."
[8]: https://github.com/Trailblaze-work/claude-prompt-trail/blob/main/README.md "claude-prompt-trail/README.md at main · Trailblaze-work/claude-prompt-trail · GitHub"
[9]: https://mcpmarket.com/tools/skills/prompt-recorder "Prompt Recorder Claude Code Skill - Document AI Intent"
[10]: https://github.com/QuesmaOrg/git-prompt-story?utm_source=chatgpt.com "QuesmaOrg/git-prompt-story"
[11]: https://github.com/r-strawser/prompt-log "GitHub - r-strawser/prompt-log: A privacy-first MCP server to log your prompts across different platforms · GitHub"
[12]: https://github.com/lucasdickey/prompt-capture-mcp "GitHub - lucasdickey/prompt-capture-mcp · GitHub"
[13]: https://github.com/tmattoneill/devctx "GitHub - tmattoneill/devctx: Claude Code forgets everything between sessions. devctx doesn't. It's an MCP server that logs what you do, tracks what's outstanding, and feeds it all back when you return — so you pick up where you left off instead of re-explaining. Persistent context, structured todos, session history, and a web dashboard to see it all. · GitHub"
[14]: https://github.com/zircote/git-notes-memory "GitHub - zircote/git-notes-memory: Git-native, semantically-searchable memory storage for Claude Code. Captures decisions, learnings, and context as git notes with vector search. · GitHub"
[15]: https://github.com/agustif/codex-conversation-cli "GitHub - agustif/codex-conversation-cli · GitHub"
[16]: https://github.com/shinshin86/codex-history-list "GitHub - shinshin86/codex-history-list: List Codex session histories from ~/.codex/sessions with cwd and first user ask. · GitHub"
[17]: https://github.com/Uri2001/codex-sessions "GitHub - Uri2001/codex-sessions: The cross-platform terminal user interface for browsing, searching, and managing Codex CLI session logs · GitHub"

