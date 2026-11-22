---
name: omniroute-management
description: Use when managing the OmniRoute AI router service.
version: 0.1.0
author: Everlily Nexus
license: MIT
metadata:
  hermes:
    tags:
      - omniroute
      - service-management
      - stop-start-restart
    related_skills:
      - termux-linux-engineer
---

# OmniRoute Management

Class-level skill for managing the OmniRoute AI router service (stop, start, restart, status, health, port conflicts, API key, chat testing).

## When to use
Any task involving the OmniRoute service: stopping it, starting it, restarting it, checking whether it is reachable, resolving port conflicts, configuring an API key, or testing chat/completions.

## Procedure (in order)

1. **Check if OmniRoute is already running before acting.**
   - Probe the default port directly: `curl -s -m 5 -o /dev/null -w "HTTP %{http_code}\n" http://127.0.0.1:20128/`
   - Run `omniroute health` — a reachable server returns an HTTP status (even 401 means it is up); a dead server gives a connection error.
   - Do NOT trust `omniroute stop`'s "Server stopped" message alone, and do NOT trust `omniroute status` to report a running server accurately.

2. **Stop OmniRoute.**
   - Run `omniroute stop`. It prints "Server stopped" whether or not it actually killed a process (port-based stop has no PID file to verify against).
   - Always verify the port is free afterward: `curl -s -m 5 http://127.0.0.1:20128/` should fail with connection refused (HTTP 000 / curl error), not return 200.
   - If the port is still occupied after `omniroute stop`, the running instance has no PID file and stop could not kill it. Investigate before starting a second instance.

3. **Start OmniRoute.**
   - Run `omniroute serve`. If port 20128 is occupied, it exits with: "Port 20128 is already in use by an unknown process. Another OmniRoute is most likely already serving there."
   - Never run a second `serve` on the same port while one is already bound — it will fail.
   - To run alongside an existing instance, use `omniroute serve --port <other-port>`.

4. **Restart OmniRoute.**
   - Prefer `omniroute restart` over manual stop+start when available.
   - If doing it manually: stop, verify port free, then serve.

5. **Test the service.**
   - Dashboard: `curl -s -m 5 -o /dev/null -w "HTTP %{http_code}\n" http://127.0.0.1:20128/` → expect 200.
   - Models catalog: `omniroute models` → returns live provider/model list when server is up.
   - Chat/completions: `omniroute chat "<prompt>"` → requires an API key; without one returns 401 `Missing API key`. Set `export OMNIROUTE_API_KEY=<key>` or pass `--api-key <key>`.
   - Direct endpoint probe: `curl -s -m 10 -X POST http://127.0.0.1:20128/v1/chat/completions -H "Content-Type: application/json" -d '{"model":"...","messages":[{"role":"user","content":"..."}]}'`

## Pitfalls

- `omniroute stop` reports "Server stopped" via port-based stop even when no PID file exists and no process was actually killed. Always verify port 20128 is free after stopping — do not proceed to `serve` until curl fails.
- `omniroute status` shows empty `server/` and `supervisor/` directories and may report no server running even when an OmniRoute instance is bound to port 20128 without a PID file. Direct port probing is the source of truth.
- `omniroute serve` fails to bind if port 20128 is already occupied. The CLI warns "Another OmniRoute is most likely already serving there" — treat this as confirmation an instance is already up, not as a reason to force-start on another port unless intended.
- OmniRoute listens on `0.0.0.0` with NO API-key requirement by default, so the inference plane (`/v1/*`) is reachable by any device on the network. On untrusted networks set `REQUIRE_API_KEY=true` or bind loopback with `OMNIROUTE_SERVER_HOST=127.0.0.1`.
- `omniroute chat` and `/v1/chat/completions` return 401 `Missing API key` when no `OMNIROUTE_API_KEY` is configured. The model catalog endpoint (`/v1/models`) may be open while chat is not — an open models endpoint does not mean chat works.

## Commands reference
- `omniroute stop` — stop the server
- `omniroute serve` — start the server (default port 20128)
- `omniroute restart` — restart the server
- `omniroute status` — show status dashboard (may be incomplete without PID file)
- `omniroute health` — check server health (reachable = up, even on 401)
- `omniroute models` — list available models (requires server)
- `omniroute chat <prompt>` — one-shot chat (requires API key)
- `omniroute config` — show/update CLI configuration
- `omniroute env` — show/manage environment variables

## Data directory
- `~/.omniroute/` — storage.sqlite, logs/, server/, supervisor/, .env
- PID files live in `~/.omniroute/server/` and `~/.omniroute/supervisor/` — often empty, so PID-file-based stop is unreliable.

