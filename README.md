# vix-antidump (Standalone FiveM Resource)

A lightweight, standalone anti-dump resource that detects suspicious client behavior commonly used by dumpers (rapid resource enumeration and file loading) and takes action:

- Disconnect the player (configurable)
- Log identifiers (IP, Steam, license, etc.) to server console
- Optional Discord webhook notification

## Files

- `antidump/fxmanifest.lua` – Resource manifest
- `antidump/config.lua` – Configurable thresholds and options
- `antidump/client.lua` – Client-side heuristics and reporting
- `antidump/server.lua` – Server-side logging and disconnect

## Installation

1. Copy the `vix-antidump/` folder into your server's `resources/` directory.
2. Edit `antidump/config.lua` as needed:
   - `DropOnDetect` – whether to disconnect on detection
   - `DropMessage` – message shown to the player
   - `Thresholds` – adjust sensitivity per native
   - `DiscordWebhook` – paste a webhook URL to enable Discord logs (or leave empty to disable)
3. Add the resource to your server config (`server.cfg`):

```
ensure vix-antidump
```

4. Restart your server.

## How it works

Client side wraps and counts calls to natives often abused during dumping:

- `GetNumResources`
- `GetResourceByFindIndex`
- `GetResourceState`
- `GetResourceMetadata`
- `LoadResourceFile`

If any call count exceeds a configurable threshold within a short rolling window, the client reports to the server with a reason and a small stack trace. An optional heuristic uses `debug.sethook` to detect extremely tight loops that execute a very high number of function calls in a single frame.

Server side receives the report, logs identifiers, optionally sends a Discord webhook, and disconnects the player if enabled.

## Commands

- In-game: `/antidumptest_client` – triggers a client-side test report from your client.
- In-game: `/antidumptest` – triggers a server-side test flow for yourself.

## Notes and Limitations

- This is a heuristic-based detector; it focuses on common behaviors of dumpers. It won't stop all possible tools or custom approaches.
- Tune `Thresholds` and `WindowSeconds` for your server. If you see false positives, raise the thresholds.
- Do not rely solely on this script for security. Combine it with proper server-side permission checks and obfuscation where needed.

## Support

If you encounter issues or want enhancements, provide console logs with the `[AntiDump]` prefix and any steps to reproduce.
