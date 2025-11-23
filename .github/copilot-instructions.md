<!-- GitGuardian / ggshield: Copilot instructions for AI coding agents --> 

This file gives concise, repo-specific guidance to help AI coding agents be immediately productive.

1. Repo purpose & big picture
- `ggshield` is a Python CLI that detects secrets (uses the `py-gitguardian` API).
- Core boundaries: CLI layer in `ggshield/` (commands live under `ggshield/cmd/`), business logic in `core/`, utilities in `utils/`, tests in `tests/`, `unit/`, and `functional/`.

2. Key files to reference
- CLI entrypoint: `ggshield/__main__.py` and `pyproject.toml` -> `[project.scripts] ggshield = "ggshield.__main__:main"`.
- Command implementations: `ggshield/cmd/*` (e.g. `ggshield/cmd/auth/login.py`).
- Client & scan logic: `core/client.py`, `core/scan/` and related helpers in `core/`.
- Packaging/version: `pyproject.toml` (`tool.pdm.version` reads version from `ggshield/__init__.py`).
- Tests: top-level `tests/` (functional), `unit/` (unit tests). See `tool.pytest.ini_options` in `pyproject.toml` for test runner options.

3. Developer workflows (commands & examples)
- Setup dev environment: use PDM (devcontainer uses `pdm install`). Example:
  - `pdm install` (installs the project)
  - To install test/dev groups: `pdm install -G tests,dev`
- Run tests:
  - `pdm run pytest` (runs tests as configured by `pyproject.toml`)
  - Tests use `--pdbcls=IPython.terminal.debugger:Pdb` and may drop into debugger on failure.
- Run the CLI locally:
  - `pdm run ggshield --help` or `pdm run python -m ggshield`
  - Example scan: `pdm run ggshield secret scan path -r .`
- Lint / format: project uses `black` and `isort` settings in `pyproject.toml` (line length 88). Run `pdm run black .` before committing.
- Pre-commit: repository contains `pre-commit-config.local-example.yml`; team uses `pre-commit install` (devcontainer runs this).

4. Project-specific patterns & conventions
- Use `ggshield/cmd` modules to add CLI subcommands; prefer adding command-specific logic in `core/` and keep commands small (parsing/options -> delegate work).
- Version and build: `tool.pdm.version` points to `ggshield/__init__.py` for the canonical version string — update there when releasing.
- Packaging constraints: `pyproject.toml` pins `pyinstaller` and forces an `urllib3` override for compatibility — be cautious when updating transitive dependencies.
- Tests: `tool.pytest.ini_options` includes IPython PDB and timing flags; expect interactive behavior in failing tests.

5. Integration points & external dependencies
- Uses `py-gitguardian` to call GitGuardian public API (see dependencies in `pyproject.toml`).
- Docker artifacts and CI: `docker/` and `scripts/` contain packaging/dev scripts; functional tests may rely on Docker (see `tests/functional/test_docker_image.py`).

6. What to look for when modifying code
- Keep CLI UX and outputs stable (this is a widely-distributed CLI). Avoid changing output formats without tests.
- Secrets handling: README explicitly states secrets are not stored; do not add code that logs secret contents.
- When adding features, add unit tests under `unit/` and functional tests under `tests/functional/` as appropriate.

7. Quick pointers for common tasks (copyable)
- Dev container: see `.devcontainer/devcontainer.json` — it runs `pdm install` and `pre-commit install`.
- Run unit tests only: `pdm run pytest unit/`.
- Run functional tests: `pdm run pytest tests/functional/` (may require Docker).

8. Where to find more context
- User-facing docs and examples: `README.md` (root) and `doc/`.
- Test fixtures & factories: `tests/factories.py`, `unit/conftest.py`, `tests/conftest.py`.

If anything here is unclear or you want deeper coverage of a specific subsystem (CLI, packaging, or tests), say which area and I will expand with concrete examples and additional file references.
