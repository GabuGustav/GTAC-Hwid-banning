# hwid-banning

Basic, admin-only HWID banning resource for GTA Connected (GTA IV), built as a clean starter you can extend later.

## Overview
`hwid-banning` provides a simple hardware-fingerprint ban flow:
- derive a best-effort fingerprint for players
- allow trusted admins to ban/unban by fingerprint
- block banned players on join
- persist bans and logs to JSON files

This resource is intentionally minimal and safe to iterate on.

## Files
- `meta.xml` - resource metadata
- `server.js` - core logic and commands
- `config.json` - command levels and behavior toggles
- `hwid_bans.json` - persisted ban entries
- `hwid_log.json` - action/event log (created automatically)

## Commands
- `/hwidwhoami` (level 1)
  - Shows your current derived fingerprint.
- `/hwidban <id:name:ip:token> [reason]` (level 2)
  - Bans a target player using their current derived fingerprint.
- `/hwidunban <hwid-fragment|name|token>` (level 2)
  - Removes matching entries from the HWID ban list.
- `/hwidbanlist` (level 1)
  - Shows current ban entries (trimmed output).
- `/hwidreload` (level 2)
  - Reloads config and ban data from disk.

## Admin Authorization
This resource uses trusted admin checks in this order:
1. `JobsRP.Auth.requireTrustedAdmin` (if available)
2. fallback to v-admin style checks (`b.admin` + `b.admin.auth`)

If neither path grants trusted admin status, commands are denied.

## Fingerprint Strategy (Current)
The resource builds a best-effort fingerprint from available signals:
- `serial` / `guid` (if exposed)
- `b.token` (from v-admin)
- IP
- player name

Signals are concatenated into a single fingerprint string and matched exactly.

## Configuration (`config.json`)
- `commandLevels` - required level per command
- `disconnectMessage` - message shown before disconnect
- `allowConsoleBypass` - allow server console override
- `requireTrustedAuth` - require trusted auth checks
- `autoBanOnJoinCheck` - enforce checks at join
- `maxLogEntries` - max entries kept in log file

## Data Format
`hwid_bans.json` entries include:
- `hwid`
- `name`
- `ip`
- `token`
- `reason`
- `bannedBy`
- `bannedAt`

## Limitations (Important)
This is not anti-cheat grade yet.
- Hardware/fingerprint data can be spoofed.
- IP and name are weak identifiers.
- Exact-match fingerprinting is simple, not risk-scored.

Use this as a moderation layer, not a sole security boundary.

## Setup
1. Ensure `resources/hwid-banning` is present.
2. Add/start the resource in `server.xml`.
3. Ensure your admin framework is active (`jobs_rp` trusted auth or `v-admin`).
4. Use `/hwidwhoami` and `/hwidbanlist` to sanity-check runtime behavior.

## Recommended Next Improvements
1. Add temporary HWID bans with expiry.
2. Add ban search by account history and previous tokens.
3. Add multi-signal confidence scoring (instead of strict exact match).
4. Add richer audit trail (JSON + export command).
5. Add optional webhook notifications for ban/unban events.
# GTAC-Hwid-banning
