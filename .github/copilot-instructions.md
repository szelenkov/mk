Repository overview

This is the original ActiveState `mk` project: a Python-based Makefile replacement. The package is in `mklib/`. The CLI entry point is the `mk` console script (setup.py entry point -> mklib.runner:main).

Build / install

- Install editable (dev) build: python -m pip install -e .
- Create a source distribution: python setup.py sdist
- Release to PyPI (helper task in Makefile.py): run the `pypi_upload` Makefile task (see below) or: python setup.py sdist --formats zip upload

Running the project's Makefile tasks (developer Makefile.py)

- This repository contains a top-level Makefile.py with helper tasks (test, sdist, pypi_upload, cut_a_release, clean, etc.).
- Recommended: install the package in editable mode (pip install -e .) and run: mk <task>
  Examples:
    - mk test            # runs the test task defined in Makefile.py
    - mk sdist           # create sdist using the Makefile.py helper
    - mk pypi_upload     # upload to PyPI (Makefile task wraps setup.py upload)

Running tests

Tests are in the `test/` directory and use the repository's custom test harness (testlib). The top-level test entry script is test/test.py.

- Run full test suite (from repo root): python test/test.py
- List available tests: python test/test.py -l
- Run a single test module: python test/test.py test_mk
- Run a specific test by its shortname (use the list output to get shortnames). Shortnames are of the form module/class/method; pass them as a single argument with slashes. Example:
    python test/test.py module/class/method
  (The harness will split on '/' into tags; all parts must match the test's tags.)

Notes about running tests:
- The harness supports tag-based filtering. Implicit tags include module name, normalized module (strip leading 'test_'), test case class, and test method names. Use negative tags by prefixing with '-'.
- You can pass logging flags: -v (verbose), -d (debug), -q (quiet).

Linting

- No repository-wide linter configuration is included. There is no flake8/black/tox config. If needed, run e.g. pip install flake8 and then: flake8 mklib/ test/

High-level architecture (big picture)

- mklib/: core package implementing a Python-based Makefile system.
  - runner.py: CLI entrypoint and argument parsing; sets up logging and invokes TaskMaster.
  - taskmaster.py / tasks.py: central task orchestration and Task/File primitives.
  - makefile.py: helpers for locating and loading Makefile.py files and include() support.
  - sh.py: shell utilities used by tasks.
  - configuration.py, common.py, utils.py: supporting utilities.
- Makefile.py (repo root): example/actual Makefile-style task definitions for this project (includes release helpers like cut_a_release, sdist, pypi_upload, test).
- test/: custom test harness (testlib.py) and unit/doctest modules used to validate behaviour.
- setup.py: packaging metadata and entry point (mk -> mklib.runner:main).

Key conventions and repo-specific patterns

- Python 2-era code: this codebase uses Python 2 idioms (print statements, raw_input, octal literals like 0666, basestring). Use a Python 2.7 environment when running tests or the CLI unless porting first.
- Makefile tasks: tasks are defined as subclasses of mklib.tasks.Task in project Makefile.py files. Include other makefiles using include(path, namespace). Namespacing is used to expose included tasks under a prefix.
- Version info: package version is declared in mklib.__version_info__ / __version__ (update these for releases). Makefile.py's cut_a_release expects CHANGES.md top section to match mklib.__version__ and will update __version_info__ when preparing next dev version.
- Test harness: tests use testlib.harness; tests are selectable via tags and shortnames. Test modules may expose a test_cases() hook to generate testcases dynamically (see test/test_mk.py).
- Logging: runner.setup_logging provides a custom "mk" formatter. Task-specific loggers follow the name prefix mk.task.<taskname>.

Files to check when changing behavior

- mklib/runner.py, mklib/taskmaster.py, mklib/tasks.py for task execution flow.
- Makefile.py (repo root) for release and dev helper tasks.
- test/ and testlib.py for running and adding tests; new tests should be discoverable by the harness.

Other AI assistant configs

- No CLAUDE.md, AGENTS.md, .cursorrules, .windsurfrules, CONVENTIONS.md, or AIDER_CONVENTIONS.md files were found. Nothing to incorporate.

Summary

Created this repo-specific Copilot instructions file describing how to build, run tests (including single tests), the big-picture architecture, and key conventions (especially Python 2 target). If you want, configure an MCP server for browser-based tests (Playwright) or add linter/CI examples; tell me which and I will add it.
