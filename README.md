# mkw-race-tracker

> A single-file command-line tool that, while a Retro Rewind race is running, attaches to the `RetroRewind.exe` process, reads the race state through the community-documented `Racedata`/`Raceinfo`/`MenuData` pointer chain, classifies each event as single-player offline, and appends one JSON Line per completed single-player race to a log file — replacing manual note-taking during Grand Prix and Time Trial sessions.

[![License](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENSE)

## Description

A single-file command-line tool that, while a Retro Rewind race is running, attaches to the `RetroRewind.exe` process, reads the race state through the community-documented `Racedata`/`Raceinfo`/`MenuData` pointer chain, classifies each event as single-player offline, and appends one JSON Line per completed single-player race to a log file — replacing manual note-taking during Grand Prix and Time Trial sessions.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Installation

Prerequisites: Python 3.12+ and Windows 10/11 64-bit (to attach to `RetroRewind.exe`).

```bash
pip install py-memory-editor
```

## Usage

```bash
python mkw_race_tracker.py
# Attaches to RetroRewind.exe (read-only), logs each completed single-player race
# as a JSON Line to ./logs/<timestamp>.jsonl
```

## Security

Security policy to be added; see SECURITY.md when available.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for development setup, coding conventions, and the pull request process.

## License

GNU General Public License v3.0 - see [LICENSE](LICENSE) for details.

## Credits

- Standard Readme spec - https://github.com/RichardLitt/standard-readme
- shields.io - https://shields.io/

> Guidance from `references/readme-guide.md` (standard-readme + shields.io). This README is a
> starting skeleton; enrich from PLAN.md before publishing.
