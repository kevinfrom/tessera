# Tessera

A tick-based, tile-grid multiplayer game written in Go.

Tessera is a **learning project**. It is not affiliated with, endorsed by, or connected to
Jagex Ltd. or RuneScape in any way, contains no Jagex assets or data, and is not a private
server for any existing game. It is an original implementation built to explore how
tick-based multiplayer servers work.

## What it does

- A fixed **600ms server tick** drives all simulation. Clients send *intents*; the server
  is authoritative for everything.
- Players move one tile per tick on a 2D grid, fight monsters, and pick up loot.
- **Multiple world servers** run the same game against **shared player data**. Your
  character is one character — it follows you between worlds, and can only exist on one at
  a time.

The interesting part is that last point. Two servers holding the same player is how you
duplicate items, so ownership is enforced with a database claim and a self-healing lease.

## Layout

```
auth/     authentication, account creation, world list
world/    world server — tick loop, game state, client connections
client/   game client (Ebitengine)
shared/   protocol, game data types, anything imported by more than one binary
data/     map chunks, item and monster definitions (JSON)
```

`auth` and `world` are servers; `client` is a desktop binary. All three import `shared`,
which is what keeps the wire protocol from drifting between them.

## Stack

| | |
|---|---|
| Language | Go |
| Transport | WebSocket |
| Encoding | msgpack |
| Database | PostgreSQL (pgx, sqlc, goose) |
| Client | Ebitengine |
| Map authoring | Tiled, converted to chunk files at build time |

## Running it

Requires Go and Docker.

```bash
docker compose up -d      # Postgres
make migrate              # apply migrations
make run-auth             # authentication server
make run-world            # world server
make run-client           # game client
```

Run `make run-world` twice with different config to bring up a second world.

```bash
make test                 # full test suite, no database or network needed
make generate             # regenerate sqlc code
```

## How it fits together

1. The client authenticates against **auth** and receives a signed token plus the world
   list, with each world's address, status and player count.
2. The player picks a world. The client connects **directly** to that world server over
   WebSocket and sends the token.
3. The world verifies the token locally using auth's public key — no callback — then
   *claims* the player in the database and loads their row.
4. Each tick, the world applies queued intents, advances the simulation, and sends every
   player a snapshot of what they can see plus any events addressed to them.
5. Logging in elsewhere kicks the existing session: the old world saves, releases the
   claim, and only then does the new world load.

Auth going down stops new logins but does not interrupt anyone already playing.

## Design rules

These are load-bearing. Breaking them causes bugs that are painful to find.

- **The tick goroutine owns all game state.** Connection goroutines decode intents onto a
  channel and write from another. Nothing else touches world state, so there are no
  mutexes on it.
- **Every timer is a tick count**, never wall-clock. Attack cooldowns, respawns, item
  despawn. This is what makes the simulation deterministic and testable.
- **The server is authoritative.** The client renders and sends intents. It is never
  trusted about position, damage, or identity.
- **Dependencies are interfaces, injected from `services.go`.** No globals, no singletons,
  no `time.Now()` in game logic. The clock, the RNG and the stores all have test
  implementations, which is why the suite runs without a database or a socket.
- **Postgres holds only mutable data.** Items, monsters, loot tables and the map are JSON
  loaded at boot.

## Testing

Simulation tests build a world from fakes, step a manual clock, and assert exact state.
A 500-tick fight resolves in microseconds. Randomness is injected, so loot and combat
tests run under a fixed seed and assert exact outcomes rather than ranges.

`world/` also ships a scripted client — a headless binary that runs a fixed intent
sequence and asserts on what comes back. It is the fastest way to reproduce a protocol
bug without opening the game.

## Contributing

Issues are grouped into epics; see CONTRIBUTING.md for the layout conventions and how to
add a service. Anything labelled `good-first-issue` is self-contained and has obvious
tests — the pathfinder, inventory stacking and loot distribution are good places to start.

## Assets

Art is CC0 or otherwise freely licensed. See ASSETS.md for per-asset attribution.

## Licence

MIT. See LICENSE.