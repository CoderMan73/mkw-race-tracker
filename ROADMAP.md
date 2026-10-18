# mkw-race-tracker Roadmap

## Project Goals

Eliminate manual note-taking by automatically capturing the data a player actually cares about
for a single-player race record — which track, grid and finish position, each finish time, who
drove which character/vehicle, and the mode — nothing beyond that. The purpose is strictly
single-player logging.

## Project Non-Goals

- VR delta tracking, lobby occupancy, private-vs-worldwide metrics.
- Multi-player (VS, Battle, online) race logging.
- Network-capture tooling (`mkw-ana`) — not used; only external `ReadProcessMemory`.
- In-game Discord Rich Presence integration.
- NTSC/PAL/J region offset configuration until validated against mkwiiki address tables.

## Shipped

(none yet)

## Current Objectives

- [ ] M1: Process discovery and attachment — attach to `RetroRewind.exe` read-only (`PROCESS_VM_READ`), resolve PID/handle, retry until process appears, close handle when it disappears, fail fast on access denied.
- [ ] M2: Address resolution — resolve the Wiicompiled MEM1 base offset empirically (RESEARCH.md §6.1); do not hardcode a literal mapping until validated.
- [ ] M3: Pointer chain and struct reads — dereference `Racedata`, `Raceinfo`, `MenuData` sInstance pointers; read scenarios/settings, players, positions, finish times; byte-swap PowerPC big-endian.
- [ ] M4: Race boundary detection and single-player classification — use `MenuData`/scene state to detect start/end; classify via `gameMode` + `localPlayerCount`.
- [ ] M5: Result assembly and JSON Lines logging — assemble the record on single-player race end, append one JSON Line to `./logs/<timestamp>.jsonl`.
- [ ] M6: Region flag configuration — expose `--region pal|ntscu|ntscj|ntsc-k`, default PAL.

## Planned

- Full test suite with mock process-memory reads.
- Packaging as a pip-installable wheel.

## Out of Scope

- NTSC offsets — deferred to a region-configuration task (RESEARCH.md §6.3).
- VR delta, lobby occupancy, private-vs-worldwide — explicitly out of scope (CONCEPT.md).
- Network-capture or in-game modification tooling.
- Online/multi-player race logging — single-player only.

## Aspirational Goals

- Validated NTSC/PAL/J region offset tables from mkwiiki.
- Optional Linux reader (`/proc/<pid>/mem`) for non-Windows platforms.
- Character/vehicle/track-name resolution from the community "List of Identifiers" (tockdom.com).

> Guidance from `references/roadmap-guide.md`. Populate from PLAN.md before first release.
