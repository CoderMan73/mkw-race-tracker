# AGENTS.md - mkw-race-tracker

## Agent Role

Specialist maintainer of **mkw-race-tracker**. Prioritize correctness of the integration glue, keep
third-party/externally-supplied content out of the repo (see Boundaries > Never), and verify
each milestone against PLAN.md (tracked in the tracking workspace, not in this repo).

## Tech Stack

| Technology    | Version | Purpose                                              |
|---------------|---------|------------------------------------------------------|
| Python        | 3.12    | Language; stdlib only for logging/CLI/struct         |
| PyMemoryEditor| v3.4.0  | Read-only process memory access (`ReadProcessMemory`)|
| struct        | stdlib  | PowerPC big-endian decoding (byte-swap)              |
| pytest        | TBD     | Testing framework (dev dependency, not yet added)    |

From PLAN.md "Target stack and tooling".

## Key Commands

```bash
python mkw_race_tracker.py   # run the tracker (attaches to RetroRewind.exe)
python -m pytest -v          # run tests
python -m pytest --cov       # run tests with coverage
python -c "import mkw_race_tracker"  # syntax/import check
```

## Architecture

Single-file CLI tool (`mkw_race_tracker.py` at repo root) that:

1. Discovers and attaches to `RetroRewind.exe` via `PyMemoryEditor.OpenProcess(name="RetroRewind.exe")` with **read-only** access (`PROCESS_VM_READ` on Windows).
2. Resolves the Wiicompiled MEM1 base address by inspecting the live process's virtual memory
   layout for a readable region containing a known MEM1 marker (e.g., game-code header pattern),
   then computes and caches the host base offset once. Does **not** hardcode a literal `0x809BD728`
   mapping until empirically validated.
3. Dereferences the `Racedata` / `Raceinfo` / `MenuData` pointer chain (sInstance pointers at
   their Wii addresses) to read `courseId`, `gameMode`, `localPlayerCount`, player
   `characterId`/`vehicleId`, final positions, and `raceFinishTime`.
4. Byte-swaps every multi-byte value (PowerPC big-endian → x86 little-endian) per the
   Dolphin autosplitter pattern.
5. Uses `MenuData`/scene state to detect race start/end boundaries and classifies the race as
   single-player via `gameMode` + `localPlayerCount`: logs only Grand Prix (mode 0) and
   Time Trial (mode 2); skips VS/Battle/online.
6. On single-player race end, assembles a JSON record and appends one JSON Line to
   `./logs/<timestamp>.jsonl`.

The program performs **exactly two** kinds of I/O against `RetroRewind.exe`:
`ReadProcessMemory` (to read the pointer chain and struct fields) and `CloseHandle` on shutdown.
No bytes are ever written to the target process.

Directories:
- `mkw_race_tracker.py` - Single-file source (all logic in one module).
- `tests/` - Test suite (pytest).
- `src/mkw-race-tracker/.gitkeep` - Reserved for future package layout.
- `logs/` - Generated output (gitignored); JSON Lines appended per session.
- `ai-docs/` - AI field notes, decisions, lessons learned.
- `docs/` - Human-facing documentation only.

## Code Style and Conventions

- Python 3.12, PEP 8 style.
- Self-documenting code; avoid inline comments unless explaining non-obvious behavior
  (e.g., PowerPC big-endian byte-swap, pointer-chain offsets, Wii address math).
- No hard-coded paths or credentials; all I/O targets the local log directory only.
- Read-only memory access only: `PROCESS_VM_READ` on Windows; never call `WriteProcessMemory`,
  inject, suspend, or otherwise modify `RetroRewind.exe`.
- Commit messages: imperative mood, present tense, first line ≤ 50 chars, body wrapped at 72.

## Testing

- **Framework**: pytest (dev dependency; not yet added to requirements)
- **Run all**: `python -m pytest -v`
- **Coverage target**: 80% post-MVP; milestones 1–2 are pure read/poll logic (mockable)

## Boundaries

### Always
- Keep externally-supplied content (code, assets, or redistributable runtimes bundled from third parties) out of the repo and out of any built artifacts.
- Record non-obvious findings in `ai-docs/decisions/` or `ai-docs/notes/`.

### Ask First
- Creating a remote repository or pushing tags/releases.
- Changing the project license or adding dependencies with non-open runtimes.

### Never
- Bundle third-party code, assets, or redistributable runtimes in the repo.
- Write to, suspend, inject into, or otherwise modify `RetroRewind.exe` — reads only (`PROCESS_VM_READ`).
- Hardcode paths or credentials; store user file-chooser state under the config directory.

## Context Loading

| Task          | Read                                                                                                               |
|---------------|--------------------------------------------------------------------------------------------------------------------|
| Start work    | PLAN.md (tracking workspace: `E:\Coding_Projects\future-projects\public/active/mkw-race-tracker/PLAN.md`), AGENTS.md, this file |
| Runtime docs  | `docs/`                                                                                                          |
| Field notes   | `ai-docs/`                                                                                                         |

## Gotchas

- Address-resolution for Wiicompiled is **unverified** (RESEARCH.md §6.1). Do NOT hardcode a literal
  `0x809BD738` mapping until resolved empirically against a live `RetroRewind.exe`; compute and
  cache a host base offset once, then reuse it.
- Retro Rewind closed-source anti-cheat may treat external memory reads as suspicious
  (RESEARCH.md §6.2); the read-only posture (`PROCESS_VM_READ` only, no writes/injection) is
  the conservative mitigation, but impact is unknown until tested against a live build.
- `MenuData`/`raceFinishTime` pointer validity windows are community-attested, not independently
  verified (RESEARCH.md §6.4); race-end boundary detection may need tuning against the autosplitter's
  scene handling.
- NTSC offsets, character/vehicle/track-name lookup tables are NOT validated (RESEARCH.md §6.3);
  deferred to a region-configuration task, not built into the single-player MVP.
- Only single-player (Grand Prix mode 0, Time Trial mode 2) races are logged (CONCEPT.md scope
  boundary). VS/Battle/online are explicitly non-single-player and must not be logged.
- Fail gracefully: if expected addresses yield invalid-looking data (out-of-range courseId/gameMode,
  null player pointers), skip the record and keep polling rather than crashing.

## Local Skills

This project may define repo-local skills in `.agents/skills/`. When starting a task, scan
this folder for skills whose descriptions match the current work.

- **Discovery**: Check `.agents/skills/` at task start.
- **When to use**: Any skill whose description matches the task at hand.
- **When to suggest**: Recurring, repeatable processes worth standardizing.
- **When NOT to suggest**: One-off decisions, single-use observations (use ai-docs/).

## ai-docs

This project uses an `ai-docs/` subdirectory for field notes, architectural decisions, and
lessons learned. See `ai-docs/README.md` for guidelines on recording knowledge between
agent sessions.

> Guidance from `references/agents-guide.md`.
