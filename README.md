# to-the-point

**Read less. Do more.**

[![skills.sh](https://skills.sh/b/balewgize/to-the-point)](https://skills.sh/balewgize/to-the-point)
[![license: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](#license)

A skill that makes coding agents lead with the answer and cut the filler. Dense, not terse: the fluff goes, the substance stays.

## Install

Works with Claude Code, Codex, Cursor, Copilot, opencode, Gemini CLI, Windsurf, and any agent that reads `SKILL.md`.

```bash
npx skills add balewgize/to-the-point -g
```

`-g` installs globally (on by default in every project); omit it for the current project only. Restart your chat once so the agent finds it. `pnpx` and `bunx` work too.

## Usage

```text
normal mode            # turn off for the rest of the chat
to-the-point           # turn back on
```

Explicit format requests always win. When off, the agent writes normally but still skips filler.

## Web chats

ChatGPT, Claude, and Gemini can't load skills. Paste this into Custom Instructions:

<details>
<summary>Show prompt</summary>

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

</details>

## Example

**Prompt:** why does my `fetch` call hang?

**Without to-the-point**

> Great question! Let me help you debug this. A hanging `fetch` can have several
> causes, so let me walk through the most common ones. First, it helps to
> understand that `fetch` has no built-in timeout...

**With to-the-point**

> Most likely no timeout: `fetch` waits forever if the server never responds.
> Wrap it in an `AbortController` with `setTimeout` so the request fails instead
> of hanging, and make sure you're consuming the response body.

## License

MIT
