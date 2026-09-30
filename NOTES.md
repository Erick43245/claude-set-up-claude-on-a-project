# Notes: setting up Claude Code on this project

## CLAUDE.md: what I included and what I left out

I started from `/init` and included four short sections:

- **Description:** one line, so Claude knows this is a small Express API.
- **Commands:** `dev`, `test`, and `lint`, plus how to run a single test file or tests matching a name. I added the single-test commands because `node --test` has no script for them and they're easy to get wrong.
- **Conventions:** rules Claude could otherwise guess wrong. Use CommonJS, not ESM. Use `node:test` + `supertest`, not Jest. Access data only through `db/store.js`. Send errors as `{ error }` JSON. Route IDs are numbers, so convert `req.params.id` with `Number(req.params.id)` before comparing.
- **Architecture:** the details you only learn by reading several files. `server.js` is the entry point; it only listens when run directly, so tests import `app`. `routes/*` has one router per resource, mounted in `server.js`. `db/store.js` is the in-memory store, shared across tests, so tests shouldn't assert exact counts or IDs. Config comes from environment variables (`.env`, documented in `.env.example`).

What I deliberately left out:

- **A file-by-file listing.** Claude can see the tree itself.
- **Generic advice** like "write tests" or "handle errors". It costs context and changes nothing.
- **Setup and onboarding steps from the README.** They're for humans and are already in the README.
- **Anything from `.env`.** No secrets or real config values; `CLAUDE.md` is committed and loaded every session.

## Permission rules (`.claude/settings.json`)

- **allow:** `npm test`, `npm run lint`, `node --test`. These are read-only checks I want Claude to run constantly without a prompt each time.
- **ask:** `git push`. Pushing is outward-facing, so I want to confirm each one.
- **deny:** reading `.env`, `git push --force`, and `git push -f`.

Without the deny rules:

- Claude could read `.env`. Any real secrets there (database URLs, API keys) would end up in the conversation and possibly in logs or pasted output.
- A force-push could overwrite shared history on the remote. That's hard to undo, and the `ask` rule alone could be approved by habit.

The `Read(./.env)` rule only blocks the Read tool, not a shell command like `cat .env`, so it's a guardrail rather than a guarantee.

## Other change

The original `.gitignore` had leading spaces on every line after the first. That meant `.env` and `.claude/settings.local.json` were **not** actually ignored (checked with `git check-ignore`). I removed the whitespace, so the two files can no longer be committed by accident.

## Verification

I verified the setup with `/memory` and `/permissions` in a fresh session:

- `/memory`: the project `./CLAUDE.md` is loaded in a fresh session. The user-level `~/.claude/CLAUDE.md` exists but is empty, there's no `CLAUDE.local.md`, and auto-memory for this project has no entries yet.
- `/permissions`: lists the rules from `.claude/settings.json`: allow `npm test`, `npm run lint`, `node --test`; ask `git push`; deny `Read(./.env)`, `git push --force`, `git push -f`. There's no `.claude/settings.local.json`, so nothing overrides them locally.
