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

Same prompt, same model (DeepSeek V4.1 Flash). Both replies shown in full.

**Prompt**

```text
Task: add a retry with exponential backoff to this function.

async function fetchWithTimeout(url, ms) {
  const controller = new AbortController();
  const id = setTimeout(() => controller.abort(), ms);
  try {
    return await fetch(url, { signal: controller.signal });
  } finally {
    clearTimeout(id);
  }
}

Plan first, then show the updated function and the key change.
```

<details>
<summary><strong>Without to-the-point</strong> — 389 words</summary>

Great question! Let me walk you through adding retry with exponential backoff to your `fetchWithTimeout` function.

First, a quick bit of context: exponential backoff is a standard resilience pattern where each retry waits longer than the last. It's commonly used when talking to flaky networks or rate-limited APIs, because hammering a failing service with immediate retries usually makes things worse.

Before I show the code, here's the plan I'd suggest:

1. Add optional `retries` and `baseDelay` parameters so callers can tune the behavior.
2. Wrap the request in a loop so it can be attempted more than once.
3. On failure, wait for an increasing delay before the next attempt.
4. Throw the last error once retries are exhausted.

There are a few design decisions worth calling out. You could retry every error, or only retryable ones like timeouts, 5xx, and 429s. You could add jitter to avoid thundering-herd problems. You could also cap the delay with a `maxDelay`. I'll include the first two since they're the most important.

Here's the updated function:

```js
async function fetchWithTimeout(url, ms, { retries = 3, baseDelay = 300 } = {}) {
  for (let attempt = 0; ; attempt++) {
    const controller = new AbortController();
    const id = setTimeout(() => controller.abort(), ms);
    try {
      return await fetch(url, { signal: controller.signal });
    } catch (err) {
      if (attempt >= retries) throw err;
      const delay = baseDelay * 2 ** attempt + Math.random() * 100;
      await new Promise((r) => setTimeout(r, delay));
    } finally {
      clearTimeout(id);
    }
  }
}
```

Let me explain what changed and why:

- The `for` loop retries the whole request instead of failing immediately.
- The `AbortController` and timer are now created inside the loop, which is important. If you reused a single controller, the first timeout would abort it permanently and every retry would fail instantly.
- The delay is `baseDelay * 2 ** attempt` (300ms, 600ms, 1200ms, and so on) with a little random jitter so multiple clients don't retry in lockstep.

A couple of final notes: this will retry non-idempotent requests like POSTs too, so if that matters you should gate retries on the HTTP method. You might also want a `maxDelay` cap for large retry counts.

I hope this helps! Let me know if you'd like a version with a `maxDelay`, retryable-status checks, or TypeScript types.

</details>

**With to-the-point** — 153 words

Plan:
1. Add `retries` and `baseDelay` options.
2. Loop attempts, with a fresh `AbortController` and timer per attempt.
3. Back off `baseDelay * 2**attempt` plus jitter; throw after the last attempt.

```js
async function fetchWithTimeout(url, ms, { retries = 3, baseDelay = 300 } = {}) {
  for (let attempt = 0; ; attempt++) {
    const controller = new AbortController();
    const id = setTimeout(() => controller.abort(), ms);
    try {
      return await fetch(url, { signal: controller.signal });
    } catch (err) {
      if (attempt >= retries) throw err;
      await new Promise((r) => setTimeout(r, baseDelay * 2 ** attempt + Math.random() * 100));
    } finally {
      clearTimeout(id);
    }
  }
}
```

Key change: the controller and timer are scoped inside the loop, so each retry gets a clean signal and a full timeout. Hoisting them out would leave every retry using an already-aborted signal.

Caveat: this retries POSTs too; gate on idempotent methods if that matters.

In plan mode, the plan itself is kept: goal, approach, files, tradeoffs, risks, and open questions, then you approve before any code is written.

## Install

### Coding agents

Works with Claude Code, Cursor, Codex, Copilot / VS Code, opencode, Gemini CLI, Windsurf, and other agents that read `SKILL.md`.

```bash
pnpx skills add https://github.com/balewgize/to-the-point
```

Install globally so it is on by default in every project:

```bash
pnpx skills add https://github.com/balewgize/to-the-point -g
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
| Claude Code / Cursor / Codex / OpenCode / Copilot / Gemini CLI / Windsurf | `pnpx skills add https://github.com/balewgize/to-the-point` |
| ChatGPT / Claude / Gemini web | Paste the prompt above into Custom Instructions |
| Any other LLM | Paste the prompt above into the system prompt |

## License

MIT
