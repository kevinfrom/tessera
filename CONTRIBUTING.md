# Contributing to Tessera

Thanks for taking a look. Tessera is a learning project first and a game second — the
point is to work out how tick-based multiplayer servers fit together, in public. Questions
and "why is it done this way?" issues are as welcome as code.

## Before you start

Read **AGENTS.md**. It documents the architectural rules the codebase depends on, and it's
written for humans as much as for AI agents. A PR that breaks one of those rules will get
sent back regardless of how well it works, so it's worth ten minutes up front.

The short version:

- The tick goroutine owns all game state
- Every timer is a tick count, never wall-clock
- The server is authoritative; the client renders and sends intents
- Dependencies are interfaces injected from `services.go`
- Postgres holds only mutable data

## Getting set up

You need Go and Docker.

```bash
git clone <repo>
cd tessera
docker compose up -d      # Postgres
make migrate              # apply migrations
make test                 # should pass with no database or network
```

The test suite runs entirely on fakes — a manual clock, a seeded RNG, in-memory stores. If
`make test` needs a running server or a populated database, something has gone wrong.

To actually play:

```bash
make run-auth
make run-world
make run-client
```

Run `make run-world` a second time with different config for a second world.

## Finding something to work on

Work is tracked as **epics** and **tasks**. Each epic is an issue containing a checklist of
task issues; each task issue has its own checklist of subtasks. Epics are labelled `epic`.

Issues labelled **`good-first-issue`** are self-contained and easy to verify — pure
functions over data with obvious tests. Pathfinding, inventory stacking and loot
distribution are good entry points. They need no database, no network, and no
understanding of the tick loop.

If you want to work on something, comment on the issue first. If you want to work on
something that isn't an issue, open one before writing code — the project has a fairly
opinionated design and it's kinder to discuss the approach than to reject a finished PR.

## Making changes

1. Branch from `main`.
2. Make the change. Keep the diff focused on one thing.
3. Add tests. See below for what that means here.
4. `make test` and `go vet ./...` pass.
5. If you touched the database, `make generate` and commit the generated code.
6. Open a PR referencing the issue.

Commit messages: a short imperative summary line, and a body explaining *why* if the
change isn't obvious. No strict format.

## Testing

This project leans hard on determinism, and it's the main thing to internalise:

- Simulation tests build a world from fakes, step a **manual clock**, and assert exact
  state. A 500-tick scenario runs in microseconds.
- Randomness is **injected**. Combat and loot tests use a fixed seed and assert exact
  outcomes, not statistical ranges.
- Assert against the recording `MessageSink`, not encoded bytes.
- `world/` ships a **scripted client** — a headless binary running a fixed intent sequence
  with assertions. Use it for protocol-level behaviour.

If a test needs `time.Sleep`, the design is wrong, not the test. Raise it.

## Database changes

sqlc reads `migrations/` as its schema, so a migration and its queries must land in the
same PR:

1. Write a goose migration. Both up and down must run clean.
2. Update the sqlc query files.
3. `make generate`.
4. Commit the generated code — never hand-edit it.

## Adding a service

New collaborators follow the same pattern:

1. Define the interface **in the package that consumes it**, not beside the
   implementation.
2. Write the production implementation.
3. Write the test implementation.
4. Construct both in `services.go` — production in `NewServices`, fake in
   `NewTestServices`.
5. Inject it through constructors. Never reach for a global.

Only introduce an interface when a second implementation exists or is imminent. Pure
functions — pathfinding, inventory maths, combat rolls — are called and tested directly.
Don't wrap them.

## Assets and content

See **ASSETS.md** before adding any art, item name or monster name. Briefly: CC0
preferred, no copyleft, nothing from any other game, generic fantasy or original names
only. Every asset needs a row in the attribution table in the same PR that adds it.

## Scope

Deliberately out of scope for now: client auto-update, build version enforcement, PvP,
trading, banking, quests, anti-cheat, horizontal scaling. Good ideas, all of them — but
the proof of concept isn't finished, and speculative features make it harder to finish.

If you think something belongs in scope, open an issue and make the case.

## Recording

Parts of this project are recorded for video. Anything you contribute may end up on screen
with your username visible in the commit log or PR. If that's a problem, say so on the
issue and it won't be.

## Licence

By contributing you agree that your contributions are licensed under the MIT Licence, the
same as the rest of the project.