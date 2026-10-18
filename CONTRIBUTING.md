# Contributing to mkw-race-tracker

Thank you for your interest in contributing to mkw-race-tracker! We welcome contributions from the community.

## Table of Contents

- [Getting Started](#getting-started)
- [How to Report a Bug](#how-to-report-a-bug)
- [How to Suggest Enhancements](#how-to-suggest-enhancements)
- [Development Environment](#development-environment)
- [Coding Conventions](#coding-conventions)
- [Pull Request Process](#pull-request-process)

## Getting Started

See the [README.md](README.md) for installation and usage instructions. A single-file command-line
tool that, while a Retro Rewind race is running, attaches to the `RetroRewind.exe` process,
reads the race state through the community-documented pointer chain, and logs single-player races.

## How to Report a Bug

1. Check existing issues on the repository to avoid duplicates.
2. Open a bug report using the issue template.
3. Include: expected behavior, actual behavior, reproduction steps, environment details.

## How to Suggest Enhancements

- Use the feature request/enhancement template.
- Encourage testing of suggested enhancements before review.

## Development Environment

1. Install Python 3.12+ (https://www.python.org/downloads/).
2. Clone the repository:
   ```bash
   git clone https://github.com/CoderMan73/mkw-race-tracker
   cd mkw-race-tracker
   ```
3. Create a virtual environment and install dependencies:
   ```bash
   python -m venv .venv && .venv\Scripts\activate  # Windows
   pip install py-memory-editor pytest
   ```
4. Run the project locally to test changes:
   ```bash
   python mkw_race_tracker.py  # requires RetroRewind.exe running
   ```
5. Run tests:
   ```bash
   python -m pytest -v
   ```

## Coding Conventions

- **Commit messages**: Use the imperative mood, present tense. First line should be a concise
  summary (limit 50 characters). Wrap the body at 72 characters. Reference issues and PRs.
  ```bash
  feat: add process discovery for RetroRewind.exe

  Implements Milestone 1: polls the process table for RetroRewind.exe,
  opens it with PROCESS_VM_READ, and retries until the process appears.
  Closes #1.
  ```
- **Code style**: Follow PEP 8. Write self-documenting code; avoid inline comments unless
  explaining non-obvious behavior (e.g., PowerPC big-endian byte-swap, Wii address math).
  Read-only memory access only — never write to or inject into the target process.

## Pull Request Process

1. Fork the repository.
2. Create a branch: `git checkout -b feat/my-feature`
3. Make your changes and ensure tests pass.
4. Commit your changes with a clear, imperative message.
5. Push to your branch: `git push origin feat/my-feature`
6. Open a pull request and address review feedback.

> Guidance from `references/contributing-guide.md`. Fill from PLAN.md before publishing.
