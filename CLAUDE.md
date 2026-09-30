# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) that serves as the starter project for the Claude Code course.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with `node --watch` (port from `PORT`)
- `npm test` — run all tests with Node's built-in runner (`node --test`)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="404"` — run tests whose name matches a pattern
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint then tests on Node 22

## Conventions

- CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Tests use `node:test` + `node:assert` with `supertest`; do not add Jest or Mocha.
- Routes never touch data directly; all reads and writes go through functions exported from `db/store.js`.
- Error responses are JSON shaped `{ error: "<message>" }` with the matching status code (400 for invalid input, 404 for missing resources).
- Route IDs are numbers — convert `req.params.id` with `Number(req.params.id)` before comparing (params arrive as strings and the store compares with `===`).

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), so tests import `app` and drive it through supertest without opening a port.
- `routes/<resource>.js` — one `express.Router()` per resource, mounted in `server.js`. A new resource needs a route file plus a `app.use(...)` line there.
- `db/store.js` — in-memory stand-in for a database (seeded with two users, auto-incrementing `nextId`). State resets on restart and is shared across all tests in a process, so tests that create users affect later ones; don't assert on exact counts or IDs.
- Config comes from environment variables; `.env` is git-ignored and `.env.example` documents the keys.
