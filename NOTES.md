What I put in CLAUDE.md and what I left out

Included a one-line project description, the main commands you run locally (npm run dev, npm test, npm run lint), concise conventions Claude can act on (CommonJS; tests use node:test + supertest; convert req.params.id with Number before comparing), and a short architecture note (server.js entry, one router per resource in routes/, db/store.js handles all data access).
Left out secrets, long developer notes, and implementation details already obvious from the code to keep the file short and actionable.

Which permission rules I added and why

allow: Bash(npm test:*), Bash(npm run lint:*), Bash(node --test:*) — so Claude can run tests and lint locally to validate suggestions.
ask: Bash(git push:*) — to prompt before pushing changes.
deny: Read(./.env), Bash(git push --force:*), Bash(git push -f:*) — to prevent exposing secrets and to block destructive force-pushes. Without the deny rules an automated action or assistant could read environment secrets or perform a force-push that overwrites history.

Verification

I started a fresh Claude session, ran /memory to confirm CLAUDE.md is loaded, and ran /permissions to confirm the allow/ask/deny rules are present. The CLAUDE.md content is used when asking "How do I run the tests here?" and similar questions.
