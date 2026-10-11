# Ready-to-Publish Good First Issues

To satisfy the **GoodFirstIssue.dev** requirement:
> *At least 3 open issues with beginner-friendly labels (`good first issue`, `beginner`, `easy`, `help wanted`, etc.)*

You can copy and paste the following 4 pre-formulated issues directly into GitHub:
[New Issue on GitHub](https://github.com/MriBoyd/mcp-discovery-server-v1.2/issues/new)

---

## 📌 Issue 1: Add `--version` CLI flag and startup banner to MCP Server

- **Title**: `feat: Add --version CLI flag and startup banner to MCP Server`
- **Labels**: `good first issue`, `help wanted`, `beginner`

### Issue Body:
```markdown
### 🌟 Description
Currently, `src/mcp_server_production.py` accepts `--sse`, `--port`, and `--host`, but does not provide a `--version` flag to quickly check the running release version, nor does it log an informative startup banner summarizing active configuration on launch.

### 🎯 Motivation & Context
Adding a `--version` flag improves CLI ergonomics and enables external tools, container health checks, and automation scripts to quickly verify the installed Ansam version. A startup banner also helps users verify whether they are running in stdio or SSE mode, which Qdrant host is targeted, and the configured connection pool capacity.

### 🛠️ Proposed Solution / Steps to Implement
1. Open `src/mcp_server_production.py`.
2. Update the argument parser:
   ```python
   parser.add_argument("--version", action="version", version="Ansam MCP Discovery Server v1.2.0")
   ```
3. In the server initialization logic, add a clean startup log or console banner displaying:
   - Server name (`Ansam`)
   - Transport mode (stdio or SSE at `http://host:port/sse`)
   - Qdrant endpoint (`Config.QDRANT_URL`)
   - Max open proxy connections (`Config.MAX_OPEN_CONNECTIONS`)
4. Add a unit test verifying `--version` output using `unittest.mock` or `argparse` testing.

### 🔍 Hints & Resources
- Relevant file: `src/mcp_server_production.py`
- Python `argparse` documentation on `action="version"`: https://docs.python.org/3/library/argparse.html#action

### 📋 Acceptance Criteria
- [ ] Running `python src/mcp_server_production.py --version` prints the version string and exits cleanly with status code 0.
- [ ] Running the server displays an informative startup summary.
- [ ] Unit tests pass via `pytest`.
```

---

## 📌 Issue 2: Support automatic `.env` configuration loading with `python-dotenv`

- **Title**: `feat: Support automatic .env configuration loading with python-dotenv`
- **Labels**: `good first issue`, `help wanted`, `beginner`

### Issue Body:
```markdown
### 🌟 Description
Currently, `src/config.py` reads configuration parameters directly from `os.getenv(...)`. If a developer creates a local `.env` file (copied from `.env.example`), Python does not automatically load it unless environment variables are exported beforehand or a dotenv loader is called.

### 🎯 Motivation & Context
Allowing automatic loading of local `.env` files greatly improves local developer experience, especially for new contributors and users running Ansam locally or with custom Qdrant ports.

### 🛠️ Proposed Solution / Steps to Implement
1. Add `python-dotenv` to `pyproject.toml` dependencies (or use an optional import fallback).
2. In `src/config.py`, add:
   ```python
   try:
       from dotenv import load_dotenv
       load_dotenv()
   except ImportError:
       pass
   ```
3. Test that values specified in a `.env` file take effect when importing `src.config.Config`.
4. Add a unit test in `tests/test_config.py` that verifies `.env` values are properly loaded.

### 🔍 Hints & Resources
- Relevant file: `src/config.py`
- Existing test file: `tests/test_config.py`
- Python-dotenv documentation: https://github.com/theskumar/python-dotenv

### 📋 Acceptance Criteria
- [ ] `src/config.py` loads `.env` when present.
- [ ] Does not crash if `python-dotenv` is not installed (graceful fallback).
- [ ] Existing `tests/test_config.py` tests pass.
```

---

## 📌 Issue 3: Add retry with exponential backoff on startup for Qdrant connection

- **Title**: `feat: Add retry with exponential backoff on startup for Qdrant connection`
- **Labels**: `good first issue`, `help wanted`, `enhancement`

### Issue Body:
```markdown
### 🌟 Description
When starting the MCP Discovery Server in environments where Qdrant is starting up concurrently (e.g., inside `docker-compose` or on a cold boot), the initial connection check in `HybridSearcher` can fail immediately if Qdrant isn't ready in time.

### 🎯 Motivation & Context
Adding a simple connection retry loop with exponential backoff gives downstream services time to become ready without immediately crashing the MCP discovery server.

### 🛠️ Proposed Solution / Steps to Implement
1. Locate where `QdrantClient` connection/readiness is verified in `src/hybrid_searcher.py`.
2. Implement a retry loop with exponential backoff (e.g., 5 attempts, initial delay of 1s, doubling up to a maximum delay).
3. Log informative warning messages on retry attempts (e.g., `Waiting for Qdrant to be ready (attempt 2/5)...`).
4. If all retries fail, raise a clear error informing the user how to start Qdrant.
5. Add unit tests with mock connection failures to verify the retry and backoff behavior.

### 🔍 Hints & Resources
- Relevant files: `src/hybrid_searcher.py`, `src/config.py`
- Can use simple `asyncio.sleep` or `time.sleep` with backoff multiplier.

### 📋 Acceptance Criteria
- [ ] Connection attempts are retried up to the maximum retry count before failing.
- [ ] Clear logs indicate retry attempts and ultimate failure reason.
- [ ] Unit tests pass with `pytest`.
```

---

## 📌 Issue 4: Add runnable example scripts for SSE/HTTP and stdio MCP clients

- **Title**: `docs: Add runnable example scripts for SSE/HTTP and stdio MCP clients`
- **Labels**: `good first issue`, `help wanted`, `documentation`

### Issue Body:
```markdown
### 🌟 Description
We want to provide new users and developers with a simple `examples/` directory containing lightweight, standalone scripts that demonstrate how to interact with Ansam.

### 🎯 Motivation & Context
New users often wonder how to connect their custom AI agents or scripts to Ansam over stdio or SSE. Having well-documented, clean example scripts makes onboarding much smoother.

### 🛠️ Proposed Solution / Steps to Implement
1. Create an `examples/` directory.
2. Add `examples/stdio_client.py` demonstrating how to connect to `src/mcp_server_production.py` using the standard `mcp` client library.
3. Add `examples/sse_client.py` demonstrating how to connect to `http://localhost:8000/sse` and call `search_tools`.
4. Include a short `examples/README.md` explaining prerequisites and how to run each example script.

### 🔍 Hints & Resources
- Relevant existing files: `src/mcp_server_production.py`, `demo.py`
- MCP Python SDK documentation: https://github.com/modelcontextprotocol/python-sdk

### 📋 Acceptance Criteria
- [ ] `examples/` folder created with `stdio_client.py` and `sse_client.py`.
- [ ] Examples are clean, heavily commented, and run successfully against a local Ansam instance.
- [ ] README in `examples/` provides clear run commands.
```
