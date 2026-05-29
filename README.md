# Pytest Dagger Toolchain

A toolchain for testing Python application with automatic OpenTelemetry tracing

This toolchain automatically injects a `pytest_otel` library for test tracing visibility in Dagger TUI and Dagger Cloud. No configuration required.

**Usage:**

On a python project, using pytest as the test runner:

- initialize a dagger module if not already done: `dagger init .`
- install the `pytest` toolchain: `dagger toolchain install github.com/dagger/pytest`
- run tests: `dagger check pytest:test`. It will:
  - creates an Alpine based container with `uv` (a uv cache volume is set)
  - prepares Python for the requested version via `uv`, failing fast if it can't be resolved
  - injects the `pytest_otel` dependency for automatic test tracing (before your source is added, so it stays cached)
  - installs your project dependencies (automatically via `uv run` for `pyproject.toml` projects, or from `requirements.txt`)
  - export captured stdout, stderr, and Python logging records as OTel logs
  - run `pytest`

`pytest:test` runs against a self-contained Alpine + `uv` base, so it needs no setup. To trace pytest inside your own container instead (your Python, your dependencies, your environment), use `installPytestOtel`: pass it your container and it installs the bundled `pytest_otel` (using `uv` if present, otherwise `pip`), returning the container so you can run `pytest` yourself with tracing enabled.

Example, using `fastly/fastly-cli`:

```console
$ git clone github.com/fastly/fastly-cli

$ cd fastly-cli

$ # Initialize an empty dagger module
$ dagger init

Initialized module fastapi-cli in ~/dev/src/github.com/fastapi/fastapi-cli

$ # Add the pytest toolchain
$ dagger toolchain install github.com/dagger/pytest

toolchain installed

$ # Run tests, with tracing enabled
$ dagger check
✔ pytest:test 17.4s ⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣧⣿⣿⣿⣿⣿⣿⣿⡆⡄⣿⣿⡄ OK
```

![Dagger Cloud test output](dagger-cloud.png)
