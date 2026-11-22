---
name: codex-delegate
description: Split programming work between Claude and Codex to save Claude tokens. Use for ANY coding task in this repo (implement, fix, refactor, write tests, search a large codebase). Claude plans and reviews; Codex writes the code. If Codex is unavailable or out of quota, Claude does everything itself.
---

# Reparto de trabajo Claude + Codex

Goal: spend as few Claude tokens as possible. Codex (runs on the user's ChatGPT
account) does the heavy work; Claude coordinates and reviews.

## Who does what

| Task | Owner |
|---|---|
| Understand the request, plan, split into steps | Claude |
| Write/modify code, refactors, tests, repetitive edits | Codex |
| Broad search/reading of the codebase | Codex |
| Review the diff, decide, fix small issues | Claude |
| git commit / push / PRs, talking to the user | Claude |

Do it yourself (no delegation) when the change is trivial (a few lines in a
known file): delegating would cost more than doing it.

## 1. Check Codex is available

```bash
codex login status
```

- Not installed → `npm install -g @openai/codex`.
- Not logged in → run `codex login --device-auth` in the background, give the
  user the URL and one-time code, and wait. If the user declines, go to
  **Fallback**.

## 2. Delegate with concrete instructions

Write a precise brief: goal, files involved, constraints, how to verify
(test command), and "do not commit". Then run:

```bash
codex exec -s workspace-write -C "$PWD" -o /tmp/codex-last.txt "<brief>" > /tmp/codex-run.log 2>&1
echo "exit=$?"; cat /tmp/codex-last.txt
```

- Do NOT read the whole `/tmp/codex-run.log`; only `tail` it if something failed.
- Long tasks: split them into several smaller `codex exec` calls.

## 3. Review only the diff

```bash
git status --short && git diff --stat && git diff
```

Review the diff (not whole files). Run the tests. If something is wrong:
- small fix → fix it yourself;
- larger problem → send Codex a new `codex exec` with the concrete correction.

Then commit and push as usual (Claude does this, never Codex).

## Fallback: Claude does everything

Switch to doing the whole task yourself, without asking, when any of these
happens:
- `codex exec` output mentions a usage/rate limit (e.g. "usage limit",
  "rate limit", "429", "quota", "Too Many Requests");
- authentication errors (401, "not logged in") and the user cannot log in;
- Codex is not installed and cannot be installed, or keeps failing/timing out
  (2 failed attempts).

Tell the user in one line that Codex is unavailable (and why) and that you are
continuing on your own. Do not retry Codex again in that session unless the
user asks.
