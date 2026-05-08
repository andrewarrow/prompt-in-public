# prompt

What about an MCP tool for codex or claude code that posts your prompts publicly? The prompts I write inside there are the only value add I have these days as a human. I am rather proud of some of my prompts but no other human ever sees them. I've often thought the prompts should be part of git history now. Could be a new x/twitter that catches on. Prompt in public? - for open source repos only - can you think of a way to make this work and go viral

# answer

Yes. This is a genuinely good idea, but I would **not** position it as “an MCP tool that posts every prompt publicly.” That will scare people. I’d position it as:

> **Prompt in Public: show the human intent behind open-source agent work.**

The winning object is not “a prompt tweet.” It is a **Prompt Card** attached to a real repo, issue, PR, commit, and outcome.

Codex and Claude Code are technically ready for this now. Codex supports MCP servers in the CLI and IDE extension, and it now has hooks including `UserPromptSubmit`, where the hook receives the actual prompt text before it is sent. ([OpenAI Developers][1]) Claude Code also supports MCP, and its `UserPromptSubmit` hook receives the submitted `prompt` field before Claude processes it. ([Claude API Docs][2]) MCP itself is a good fit because servers can expose **tools**, **resources**, and **prompts**, so your service can both publish prompt artifacts and let agents search/reuse them later. ([Model Context Protocol][3])

## The product

Call it something like **PromptPub**, **Promptlog**, **Human Commit**, **PromptTrail**, or simply **Prompt in Public**.

The core unit:

```yaml
type: prompt_card
repo: owner/project
issue: "#412"
pr: "#419"
commit_before: abc123
commit_after: def456
tool: codex | claude-code
author: aa
visibility: public
license: CC-BY-4.0 or MIT
prompt_hash: sha256:...
status: tests-passed | pr-opened | merged | failed
```

Then the visible card says:

> **Prompt:** “Refactor the auth middleware so refresh-token validation is isolated, preserve all public route behavior, add regression tests for expired refresh tokens, and do not touch the OAuth provider interface until you show me a plan.”
> **Result:** 7 files changed, 4 tests added, CI passed, PR merged.
> **Used in:** `owner/project#419`
> **Remixed by:** 12 developers.

That is much more compelling than “here is a random prompt.” The social value is: **this prompt produced work**.

## The architecture I’d build

Make it three pieces, not one.

First, a **local hook package**: `promptpub init`.

For Codex, install a `UserPromptSubmit` hook and maybe a `Stop` or `PostToolUse` hook. Codex hooks can run deterministic scripts during the lifecycle, and the docs even list examples like sending conversations to logging/analytics, scanning prompts for pasted API keys, and using `UserPromptSubmit` at turn scope. ([OpenAI Developers][4])

For Claude Code, install a `UserPromptSubmit` hook to capture the prompt, and a `PostToolUse`/`Stop` hook to summarize what happened. Claude Code hooks can run on lifecycle events, and `PostToolUse` receives tool input/output after successful tool execution. ([Claude API Docs][5])

Second, an **MCP server** called `promptpub`.

It exposes tools like:

```text
publish_prompt_card
draft_prompt_card
redact_prompt
search_public_prompts
fork_prompt
cite_prompt
attach_prompt_to_pr
```

It also exposes resources like:

```text
promptpub://repo/owner/project/trending
promptpub://prompt/abc123
promptpub://author/aa
```

That lets Codex or Claude Code later say, “Find prompts that successfully fixed flaky Playwright tests in Next.js repos,” and pull in examples. This is where MCP becomes more than logging: it turns the public prompt corpus into reusable agent context.

Third, a **web/social layer**.

Not a full Twitter clone on day one. Start as GitHub-native:

```text
promptpub.dev/owner/repo/prompt/abc123
```

Every card has copy, fork, remix, run-with-Codex, run-with-Claude, and link-to-PR buttons.

## The git-history version

You are right that prompts should be part of git history, but I would be careful about polluting normal commits. I’d support three modes:

**1. Commit trailers**

Best for explicit attribution.

```text
Prompt-Id: pp_01HX...
Prompt-Url: https://promptpub.dev/p/pp_01HX...
Prompt-Hash: sha256:...
Prompt-Author: aa
```

**2. Git notes**

Best for purists. Git notes attach metadata to commits without changing the tree. Less visible, but clean.

**3. `.promptlog/` files**

Best for open-source projects that want prompts as first-class artifacts.

```text
.promptlog/
  2026-05-08/
    fix-refresh-token-tests.md
```

The `.promptlog` version is probably the one that makes the idea feel real: “This repo develops in public, including the prompts.”

But for virality, the PR comment matters more than the git object. Every PR should get a beautiful comment:

> This PR was guided by a public prompt:
> “…”
> View/fork/remix the prompt →

That is the growth loop.

## The viral mechanic

The main mistake would be making this about “prompt sharing.” Prompt sharing has been tried a lot. Most prompt libraries become graveyards because prompts without context are boring.

Make it about **status plus proof**.

The scoreboard should not be “most liked prompts.” It should be:

```text
Most merged prompts this week
Most remixed prompts
Prompts with highest test-pass rate
Best prompt for refactors
Best prompt for bug reproduction
Best prompt under 500 tokens
Best first prompt on a GitHub issue
```

That gives people a reason to care. Developers do not want another social feed. They do want to know: **who is actually good at directing agents?**

The viral loop:

1. Developer uses Codex/Claude Code on an OSS repo.
2. PromptPub drafts a Prompt Card locally.
3. Developer approves publishing after the PR/diff looks good.
4. PromptPub comments on the PR with the card.
5. Other devs click “remix this prompt.”
6. Their successful remixes backlink to the original.
7. The original author gets reputation.

That creates a new kind of developer status: not “I wrote the code manually,” but “I wrote the instruction that reliably produced good code.”

## The trust rule

Do **not** auto-post raw prompts. Ever.

Default behavior should be:

```text
Captured locally → redacted → previewed → user approves → published
```

Codex’s own telemetry docs note that user prompt content is redacted unless explicitly enabled, which is the right posture for this category. ([OpenAI Developers][6]) Claude’s MCP docs also warn that third-party MCP servers should be trusted carefully because integrations can expose users to security risks like prompt injection. ([Claude API Docs][2])

So the product needs visible safety rails:

```text
✅ public GitHub repo only
✅ explicit publish approval
✅ secret scanning
✅ no private file contents by default
✅ prompt preview before publish
✅ delete/unpublish support
✅ org/repo allowlists
✅ license selection
```

Open-source-only is a smart constraint because it gives the prompt public context: repo, issue, PR, tests, diff, maintainer response. But “open-source repo” does not guarantee “safe prompt.” People paste credentials, customer names, private roadmap hints, weird local paths, and emotional comments. Redaction is not optional.

## The MVP I’d actually ship

I’d ship the smallest version like this:

```bash
npm i -g promptpub
promptpub init
```

Then it asks:

```text
Which tools?
[x] Codex
[x] Claude Code

Publish mode?
( ) never, local only
(x) draft after each agent session
( ) ask after every prompt
```

After an agent task completes:

```text
PromptPub drafted a public Prompt Card.

Repo: aa/open-source-project
Prompt: 812 chars
Diff: 6 files, +214/-39
Tests: npm test passed
Risk: no secrets detected

Publish? [y/N/edit]
```

Then:

```bash
promptpub publish --last --link-pr
```

The first public release should not try to support every platform. Start with:

```text
Claude Code hook
Codex hook
GitHub public repos
Prompt cards
PR comments
Search/remix page
```

That is enough.

## The “make it catch on” angle

The tagline should hit the emotional truth you already said:

> **The prompt is the human part.**

Other possible lines:

```text
Show your work, including the prompt.
Good prompts deserve blame.
The commit shows what changed. The prompt shows why.
Open-source the intent, not just the diff.
A public record of human judgment in agentic coding.
```

The launch demo should be dead simple:

1. Pick a real OSS issue.
2. Write a very good prompt.
3. Let Codex or Claude Code produce the patch.
4. Open the PR.
5. Show the Prompt Card attached to the PR.
6. Say: “This is what humans do now.”

That will resonate because a lot of developers are quietly feeling what you said: their value is shifting from typing code to framing, constraining, reviewing, and steering. This gives that work a public artifact.

## My blunt take

This could work. But it will not go viral as “Twitter for prompts.” It could go viral as **GitHub-visible proof of agent-directing skill**.

Make prompts:

```text
public
attributed
forkable
linked to outcomes
ranked by real engineering results
safe by default
```

That is the wedge.

The first killer feature is not the feed. It is the PR comment:

> “This PR was built from this public prompt.”

Once maintainers start seeing that, the feed becomes inevitable.

[1]: https://developers.openai.com/codex/mcp "Model Context Protocol – Codex | OpenAI Developers"
[2]: https://docs.anthropic.com/en/docs/claude-code/mcp "Connect Claude Code to tools via MCP - Claude Code Docs"
[3]: https://modelcontextprotocol.io/docs/learn/server-concepts "Understanding MCP servers - Model Context Protocol"
[4]: https://developers.openai.com/codex/hooks "Hooks – Codex | OpenAI Developers"
[5]: https://docs.anthropic.com/en/docs/claude-code/hooks "Hooks reference - Claude Code Docs"
[6]: https://developers.openai.com/codex/config-advanced "Advanced Configuration – Codex | OpenAI Developers"

