# AGENTS.md — Working Effectively in the Aviary Repo

This document is for coding agents and contributors automating changes in this repository. It summarizes the project, expected workflow, and guardrails for safe, surgical edits.

## Project Overview

- Purpose: Aviary is NASA’s OpenMDAO-based aircraft sizing and mission analysis tool, incorporating FLOPS and GASP methods.
- Entry points: `aviary/api.py` (public API), `aviary/examples/` (usage patterns), `aviary/interface/` (Level 1/2 methods), `aviary/mission/` and `aviary/subsystems/` (model internals).
- Typical inputs: Aircraft CSVs (e.g., `aviary/models/aircraft/...`), mission phase definitions (e.g., `aviary/models/missions/...`).

## Agent Workflow Expectations

- Precision first: Make minimal, targeted changes aligned with the task. Do not refactor unrelated code.
- Use small, reviewable patches: Prefer focused commits that are easy to reason about.
- Preserve numerics: Changes must not degrade sizing, mission, or performance calculations.
- Follow existing style: Match code patterns, naming, and docstring style already present in the edited files.
- Update docs when user-facing behavior changes: Prefer concise usage notes over long essays.

## Running and Validating

- Fast tests: `testflo .` from repo root runs the short suite. Use when validating changes locally.
- Long “benchmarks” (integration tests): `python aviary/run_all_benchmarks.py`. Only run when requested due to duration.
- Examples: Quick sanity checks via `python aviary/examples/run_level1_example.py` (Level 1), or `run_level2_example.py`/`run_level3_example.py` for more control.
- Python: Minimum supported version is 3.9. See `pyproject.toml` for dependencies.

## Common Extension Points

- Public API: Add or expose user-facing functions in `aviary/api.py` only when necessary and consistent with existing patterns.
- Variable metadata: Extend or override in `aviary/variable_info/` when introducing new variables or options.
- Subsystems: Add new builders under `aviary/subsystems/...` and wire through the appropriate builders.
- Mission phases/ODEs: Extend under `aviary/mission/...` and expose through phase builders when adding new mission capability.

## Coding Guidelines

- Keep changes small and scoped. Avoid large reorganizations unless explicitly requested.
- Do not introduce new top-level dependencies without discussion; reuse existing utilities where possible.
- Respect I/O boundaries: CSV schemas in `models/aircraft/` and variable names/units must remain consistent unless the task includes migration.
- Testing philosophy: Add or update tests only where directly relevant to the change. Do not “fix” unrelated tests.
- Performance: Avoid introducing heavy per-iteration work in hot loops or OpenMDAO components.

## Documentation and Examples

- README and docs: Public-facing changes should reference or extend the docs in `aviary/docs/` when applicable.
- Examples: If adding a new user-facing feature, consider a minimal example in `aviary/examples/` mirroring existing style.

## Safety and Review

- Backward compatibility: Maintain existing behavioral contracts unless the task specifies a breaking change.
- Determinism: Keep results deterministic given the same inputs and options.
- Data files: Treat anything under `models/` as canonical inputs; avoid ad-hoc format changes.

## Repo Conveniences and Pointers

- API import surface is centralized in `aviary/api.py` for users. Avoid shadow renames at import sites; consolidate in the API when appropriate.
- Utility helpers worth knowing: `aviary.utils.*` (e.g., CSV readers, options, named values), `aviary.variable_info.*` (variables, enums, metadata).
- Contact/info: See README for documentation links and team contact/discussions.

## For Agent Runners (Meta)

- File edits: Use atomic, minimal patches. Do not add license headers or reformat whole files.
- Plans and progress: For multi-step changes, outline a short plan and update as you go.
- Commands: Prefer `rg` for fast code search. Read files in small chunks to avoid truncation.
- Avoid network installs unless the task explicitly requests environment setup.

By following these guidelines, agents can make safe, useful, and reviewable changes that respect Aviary’s architecture and users.

