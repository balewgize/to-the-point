# to-the-point

**Read less. Do more.**

Coding agents still use way too many words. `to-the-point` strips the filler so you get the plan, the answer, and the code without the preamble.

It is dense, not terse: it removes fluff but keeps the substance you need to understand and approve what is happening.

## What it does

- Leads with the answer, the plan, or the code
- Keeps a compact, readable plan before code in plan mode
- Removes filler, hedging, process narration, and trailing recaps
- Stays natural English, not broken "caveman" style
- Never drops errors, warnings, or safety notes

## Before / after

Without:

> Great question! Let me walk you through what's happening here. First, I'll take a look at your `auth.js` file... The issue is that the token expiry check is comparing in the wrong direction, which means tokens are being rejected while they are still valid. I've gone ahead and updated the function for you. Here's a summary of the changes I made: I changed the operator on line 42. I hope this helps! Let me know if you'd like me to explain anything further.

With:

> The expiry check uses `<` instead of `>`, so valid tokens are rejected.
>
> ```diff
> - if (Date.now() < token.expiresAt) return null;
> + if (Date.now() > token.expiresAt) return null;
> ```

In plan mode, the plan itself is kept: goal, approach, files, tradeoffs, risks, and open questions, then you approve before any code is written.

## Install

### Coding agents

Works with Claude Code, Cursor, Codex, Copilot / VS Code, opencode, Gemini CLI, Windsurf, and other agents that read `SKILL.md`.

```bash
npx skills add balewgize/to-the-point
```

Install globally so it is on by default in every project:

```bash
npx skills add balewgize/to-the-point -g
```

Restart your chat session after installing so the agent discovers the skill.

### Web chats and any LLM

Web chats (ChatGPT, Claude, Gemini, and similar) do not load skills. Paste this into Custom Instructions, a project prompt, or the system prompt:

```text
Be dense, not verbose. Lead with the answer, result, code, or plan. Use short,
natural English and stop when done. Cut filler, pleasantries, hedging, process
narration, restating my request, and unsolicited summaries. On coding tasks,
give a compact plan before code when planning, and lead with the code or diff
when building. Never cut accuracy, errors, security and correctness warnings,
destructive-action warnings, uncertainty, or safety notes. When I say "normal
mode", "be detailed", "verbose", "full explanation", or "turn off to-the-point",
write normally for the rest of the chat but still skip filler. When I say
"to-the-point" or "concise mode", resume. No emojis unless I ask.
```

## On by default

A skill's `description` is always loaded into the agent's context, so `to-the-point` applies automatically once installed. You do not need to invoke it.

In some agents, skill activation is model-gated. If it does not kick in, say `to-the-point` once and it stays active for the session.

## Turn off and on

Turn off for the rest of the conversation:

- `normal mode`
- `be detailed`
- `verbose`
- `full explanation`
- `turn off to-the-point`

Turn back on:

- `to-the-point`
- `concise mode`

When off, the agent writes normally but still skips filler. Explicit format requests always win.

## Compatibility

| Platform | How to use |
| --- | --- |
| Claude Code / Cursor / Codex / Copilot / opencode / Gemini CLI / Windsurf | `npx skills add balewgize/to-the-point` |
| ChatGPT / Claude / Gemini web | Paste the prompt above into Custom Instructions |
| Any other LLM | Paste the prompt above into the system prompt |

## License

MIT
