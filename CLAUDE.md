# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is **Agent O** — an enterprise orchestration agent built on the Nous Research Hermes Agent codebase (MIT licence). It has been stripped of personal/consumer features and is being extended for enterprise multi-tenant use.

- [AGENT_O.md](AGENT_O.md) — Agent O build log and progress documentation (grows over time)
- [AGENTS.md](AGENTS.md) — Full Hermes developer reference (architecture, patterns, critical rules)

## Upstream sync policy

Agent O is a fork of [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent), tracked as the `upstream` remote. We do **not** use `git merge upstream/main` — upstream still contains all the consumer adapters and UIs we deleted, so a straight merge would produce conflicts on every removed file.

**How to sync:**
1. `git fetch upstream`
2. `git diff --name-only <last-merge-base> upstream/main` — find changed files
3. Filter to files that still exist in Agent O: `while IFS= read -r f; do [ -e "$f" ] && echo "$f"; done`
4. `git checkout upstream/main -- <file>` for each file worth updating
5. Commit with a summary of what was pulled and what was skipped

**What to skip when syncing:**
- Commits scoped to `telegram`, `mattermost`, `desktop`, `dashboard`, `electron`, `tui`, `slack`, `signal`, `whatsapp` — consumer features Agent O has removed
- Files in the Pending Removal list below

**Last synced:** June 2026 — upstream/main at `bb7ff7dc3` (759 commits past our branch point `95715dcb03`). Key changes pulled: cron per-profile storage revert, MCP keepalive/late-binding fixes, Gemini/Google Cloud Code adapters, credential pool + turn context/retry improvements, new test coverage. Skipped: Dockerfile, docker-compose.yml, CLAUDE.md, AGENT_O.md, tools/terminal_tool.py (Agent O customised), removed platform tests (telegram, slack, signal, whatsapp, matrix, discord, etc.), dashboard/tui_gateway tests.

## Removed from upstream Hermes (do not restore)

The following directories and files were deliberately removed as part of the Agent O cleanup. They are not missing by accident — do not recreate them.

| Removed | Reason |
|---|---|
| `apps/` | Electron desktop app and Tauri bootstrap installer. No desktop client for enterprise deployment. |
| `web/` | React dashboard whose main surface embeds `hermes --tui` via PTY. A purpose-built Agent O admin panel will be built separately if needed. |
| `acp_adapter/`, `acp_registry/` | VS Code / Zed / JetBrains ACP integration. Not relevant to enterprise agent deployment. |
| `website/` | Docusaurus documentation site for hermes-agent.nousresearch.com. Agent O hosts its own docs. |
| `packaging/` | Homebrew formula. Agent O deploys via Docker, not Homebrew. |
| `assets/` | Banner image for the upstream README/website. |
| `.plans/`, `plans/` | Nous Research internal planning documents. |
| `nix/`, `flake.nix`, `flake.lock` | Nix packaging and dev environment. Agent O uses Docker. |
| `infographic/` | Marketing infographic assets. |
| `datagen-config-examples/` | Training data generation configs for Nous Research model training. |
| `README.ur-pk.md`, `README.zh-CN.md` | Non-English README translations. |
| `CONTRIBUTING.md` | Open-source contributor guide for the Nous Research community project. |
| `SECURITY.md` | Nous Research vulnerability disclosure policy. Agent O will define its own. |
| `hermes-already-has-routines.md` | Internal Nous Research explainer doc. |
| `constraints-termux.txt` | pip constraints for Termux (Android terminal). Not a target platform. |
| `.github/` | Nous Research CI/CD workflows, issue templates, and PR templates. Agent O will configure its own pipeline. |
| `.mailmap` | Git contributor identity map for the Nous Research open-source repo. No effect on build or runtime. |
| `optional-mcps/` | Nous-curated optional MCP server configs (Linear, n8n). Agent O will configure its own MCP servers. `hermes_cli/mcp_catalog.py` and `hermes_constants.get_optional_mcps_dir()` handle a missing directory gracefully. |
| `mini_swe_runner.py` | SWE-bench software engineering benchmark runner. Nous Research research tooling, no relevance to Agent O. Never imported by core. |
| `gateway/platforms/telegram.py`, `telegram_network.py`, `slack.py`, `signal.py`, `signal_rate_limit.py`, `matrix.py`, `email.py`, `sms.py`, `dingtalk.py`, `feishu.py`, `feishu_comment.py`, `feishu_comment_rules.py`, `feishu_meeting_invite.py`, `weixin.py`, `wecom.py`, `wecom_callback.py`, `wecom_crypto.py`, `whatsapp.py`, `whatsapp_cloud.py`, `whatsapp_common.py`, `bluebubbles.py`, `yuanbao.py`, `yuanbao_media.py`, `yuanbao_proto.py`, `yuanbao_sticker.py`, `qqbot/` | Consumer messaging platform adapters. Agent O connects via `api_server.py` (company platform REST) and `webhook.py` (non-human triggers). These 26 files/dirs are not needed. |
| `plugins/platforms/discord/`, `google_chat/`, `homeassistant/`, `irc/`, `line/`, `mattermost/`, `ntfy/`, `photon/`, `simplex/` | Consumer platform plugins. Removed alongside the built-in adapters above. |
| `gateway/assets/` | Contained only `telegram-botfather-threads-settings.jpg` — a Telegram setup guide screenshot. Removed with the Telegram adapter. |
| `hermes_cli/telegram_managed_bot.py` | Telegram bot management helper. Dead code once the Telegram adapter was removed. |
| `docs/design/profile-builder.md` | Nous Research design proposal for a dashboard-based profile wizard (never implemented). References the dashboard that was already removed. |
| `docs/plans/2026-06-09-003-fix-telegram-stream-overflow-continuations-plan.md` | Telegram-specific internal fix plan. No relevance after Telegram adapter removal. |
| `.envrc` | direnv config that auto-activated the Nix flake and watched `ui-tui/`, `website/`, `apps/`, `nix/` — all of which have been removed. |
| `ui-tui/`, `tui_gateway/` | Ink/React full-screen TUI and its Python backend. Agent O uses the plain `cli.py` REPL for developer testing. |
| `optional-skills/` | Nous Research open-source skill library. Agent O defines its own skills in `skills/`. |
| `setup-hermes.sh` | Contributor quick-start script for the Nous Research open-source project. Not relevant to Agent O deployment. |
| `docker-compose.windows.yml` | Windows Docker Compose override. Agent O targets Linux containers. |
| `package.json`, `package-lock.json` | npm workspace root for `ui-tui/`, `apps/`, and `web/` — all removed. |
| `tools/environments/ssh.py` | SSH remote execution backend. Agent O is always in Docker; remote SSH execution is not needed. |
| `tools/environments/modal.py`, `managed_modal.py`, `modal_utils.py` | Modal cloud sandbox backends. External cloud service, not used in self-hosted Docker deployment. |
| `tools/environments/singularity.py` | Apptainer/Singularity HPC container backend. No HPC use case for Agent O. |
| `tools/environments/daytona.py` | Daytona cloud workspace backend. External cloud service, not relevant. |
| `tools/environments/file_sync.py` | File sync helper used only by the removed remote backends (SSH, Modal, Singularity, Daytona). Dead code once those were removed. |

### Kept in gateway/platforms/ (non-obvious)

**`base.py`** — Core ABC (`BasePlatformAdapter`, `MessageEvent`, `SendResult`). Imported at module level by `run.py`, `stream_consumer.py`, `slash_commands.py`. Cannot be removed.

**`msgraph_webhook.py`** — Microsoft Graph inbound webhook handler. Kept because it is directly relevant to the planned Teams integration — it receives Teams/Office365 change-notification events.

**`plugins/platforms/teams/`** — Full Teams adapter (53KB `adapter.py`, Azure AD config). Kept for future Teams integration.

### Known dead code after platform adapter removal

`gateway/run.py` lines 6389–6555 contain a 20-branch `if/elif` chain dispatching to each platform adapter via lazy imports. With the adapters gone, 18 branches are dead code. **Not a runtime issue** — lazy imports mean nothing breaks. Remove this block when writing the company adapter, replacing it with a plugin registry lookup.

### Kept from upstream (non-obvious)

**`locales/`** — looks like UI translations but is a gateway runtime dependency. `gateway/run.py` imports `agent.i18n` which loads `locales/*.yaml` at startup to translate system messages sent to messaging platforms. Deleting it breaks the gateway.

**`LICENSE`** — MIT licence requires the copyright notice to be preserved in any copy or substantial portion of the software. Agent O is built on Hermes; removing this would be a licence violation.

**`MANIFEST.in`** — tells setuptools which non-Python files to include in a source distribution. Ensures `locales/`, `skills/`, and plugin manifests are bundled in any pip-installable build.

**`.hadolint.yaml`** — Dockerfile linter config with documented rule suppressions that still apply to the Agent O Dockerfile.

## Explicitly kept for Agent O

### CLI and display layer

`cli.py`, `hermes` (entrypoint), `hermes_cli/`, `hermes_bootstrap.py` — the developer-facing terminal interface. Used for local testing and debugging only; enterprise users connect via the gateway.

Includes the display/aesthetics layer: `hermes_cli/skin_engine.py` (CLI theming), `hermes_cli/banner.py`, `agent/display.py` (KawaiiSpinner, tool output feed). These are imported by `cli.py` and `agent/display.py` and cannot be easily removed without surgery — and there's no reason to since this is how the developer interacts with Agent O locally.

> **Not the TUI.** `hermes --tui` (the full-screen Ink/React UI) requires `ui-tui/` and `tui_gateway/` which are being removed. `hermes` (no flag) runs the plain `cli.py` REPL — no Node.js required.

---

### Core agent engine

The ReAct loop and everything it depends on. Nothing here is negotiable.

| Component | Files |
|---|---|
| Agent loop | `run_agent.py` — `AIAgent` class, ReAct loop, system prompt builder, context compression trigger |
| Tool orchestration | `model_tools.py` — `discover_builtin_tools()`, `handle_function_call()` |
| Tool registry | `tools/` — 80+ tools, `tools/registry.py` — self-registration pattern |
| Toolset gating | `toolsets.py`, `toolset_distributions.py` — controls what tools the agent can see |
| Agent internals | `agent/` — provider adapters, memory manager, context compressor, auxiliary LLM client, curator, display |
| Session persistence | `hermes_state.py` — SQLite + FTS5, every message and tool call stored |
| Path resolution | `hermes_constants.py` — `get_hermes_home()` / `display_hermes_home()` — all HERMES_HOME paths go through here |
| Logging | `hermes_logging.py` — `agent.log`, `errors.log`, `gateway.log` |
| Skills system | `skills/`, `tools/skills_tool.py`, `tools/skills_hub.py` — skill library + read/write/manage |
| Scheduler | `cron/` — `jobs.py` + `scheduler.py`, runs full agent pipeline on a timer |
| Fine-tuning pipeline | `batch_runner.py`, `trajectory_compressor.py` — generate and process training trajectories from Agent O runs for LLM fine-tuning on tool-calling behaviour |

---

### Gateway framework

`gateway/` is fully kept — the orchestration layer that routes inbound messages to `AIAgent` and sends responses back. Platform adapters were stripped; the core machinery stays.

| Kept in `gateway/platforms/` | Purpose |
|---|---|
| `base.py` | `BasePlatformAdapter` ABC — every adapter (including the company one) extends this |
| `api_server.py` | OpenAI-compatible REST endpoint — **primary connection surface for the company platform** |
| `webhook.py` | Inbound webhooks — **non-human triggers** (business events, monitoring, inter-agent escalation) |
| `msgraph_webhook.py` | Microsoft Graph change-notification webhook — **future Teams integration** |
| `helpers.py`, `_http_client_limits.py` | Shared gateway utilities |

`plugins/platforms/teams/` — full Teams adapter kept for future integration.

---

### Terminal backend

Agent O is always deployed in a Docker container. `tools/environments/` retains only:

- **`local.py`** — executes shell commands inside the container Agent O runs in. This is the active backend.
- **`docker.py`** — kept pending a decision on whether Docker-in-Docker is needed for sandboxed task execution.

Removed: `ssh.py`, `modal.py`, `managed_modal.py`, `modal_utils.py`, `singularity.py`, `daytona.py`, `file_sync.py` — remote and cloud execution backends not relevant to a self-hosted Docker deployment.

---

### Memory providers

`plugins/memory/` contains pluggable memory backends: honcho, mem0, supermemory, byterover, hindsight, holographic, openviking, retaindb. Each implements the `MemoryProvider` ABC from `agent/memory_provider.py`.

These are kept as **reference implementations only**. None of them are active in Agent O — they are personal-use providers that do not support per-tenant, per-user memory isolation. They serve as integration patterns and API examples for when a custom enterprise memory module is built as a standalone plugin.

---

## Development Setup

```bash
# Create venv and install all extras
uv venv .venv --python 3.11
source .venv/bin/activate
uv pip install -e ".[all,dev]"

# Config
mkdir -p ~/.hermes/{cron,sessions,logs,memories,skills}
cp cli-config.yaml.example ~/.hermes/config.yaml
touch ~/.hermes/.env
echo "OPENROUTER_API_KEY=***" >> ~/.hermes/.env
```

## Build & run (Docker)

Agent O is always deployed as a Docker container. The image is Python-only — no Node.js, npm, or browser runtime.

```bash
# Build
docker build -t agent-o:baseline .

# Run (gateway mode, REST API on port 8642)
docker run -d --name agent-o \
  -e HERMES_UID=$(id -u) -e HERMES_GID=$(id -g) \
  -e API_SERVER_HOST=0.0.0.0 -e API_SERVER_PORT=8642 \
  -e API_SERVER_KEY=<your-token> \
  -e OPENROUTER_API_KEY=<your-key> \
  -v ~/.hermes:/opt/data -p 8642:8642 \
  agent-o:baseline gateway run

# Or with docker compose (reads OPENROUTER_API_KEY and API_SERVER_KEY from ~/.hermes/.env)
HERMES_UID=$(id -u) HERMES_GID=$(id -g) docker compose up -d
```

**Health check:**
```bash
curl -s http://localhost:8642/health
# -> {"status": "ok", "platform": "hermes-agent", "version": "..."}
```

**Send a prompt:**
```bash
curl -s http://localhost:8642/v1/chat/completions \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"model":"hermes-agent","messages":[{"role":"user","content":"Hello"}]}'
```

## Running Tests

Always use the wrapper script — never call `pytest` directly. It enforces a hermetic env (all API keys unset, TZ=UTC, LANG=C.UTF-8, per-test subprocess isolation) that matches CI.

```bash
scripts/run_tests.sh                                   # full suite
scripts/run_tests.sh tests/gateway/                    # one directory
scripts/run_tests.sh tests/agent/test_foo.py::test_x   # single test
scripts/run_tests.sh tests/foo.py -- --tb=long         # pass pytest flags
scripts/run_tests.sh --no-isolate tests/foo/           # disable isolation (debug only)
```

Every test runs in a fresh subprocess via `tests/_isolate_plugin.py`. The `_isolate_hermes_home` autouse fixture redirects `HERMES_HOME` to a tmp dir — never write to `~/.hermes/` from tests.

When testing profile features, also mock `Path.home()`:
```python
@pytest.fixture
def profile_env(tmp_path, monkeypatch):
    home = tmp_path / ".hermes"
    home.mkdir()
    monkeypatch.setattr(Path, "home", lambda: tmp_path)
    monkeypatch.setenv("HERMES_HOME", str(home))
    return home
```

## TUI Dev Commands

```bash
cd ui-tui
npm run dev        # watch mode
npm run build      # full build
npm run type-check # tsc --noEmit
npm run lint       # eslint
npm run fmt        # prettier
npm test           # vitest
```

## Architecture

### Core Execution Flow

```
User message → AIAgent._run_agent_loop()  (run_agent.py)
  ├── Build system prompt  (agent/prompt_builder.py)
  ├── Call LLM via OpenAI-compatible API
  ├── If tool_calls: dispatch via registry → add results → loop
  ├── If text: persist session to SQLite → return
  └── Context compression if approaching token limit  (agent/context_compressor.py)
```

### File Dependency Chain

```
tools/registry.py   (no deps — imported by all tool files)
       ↑
tools/*.py          (each calls registry.register() at import time)
       ↑
model_tools.py      (triggers tool discovery via discover_builtin_tools())
       ↑
run_agent.py, cli.py, batch_runner.py, environments/
```

### Key Entry Points

| File | Role |
|------|------|
| `run_agent.py` | `AIAgent` class — core conversation loop (~12k LOC) |
| `cli.py` | `HermesCLI` — interactive CLI with prompt_toolkit (~11k LOC) |
| `model_tools.py` | Tool orchestration, `handle_function_call()` |
| `toolsets.py` | Toolset definitions, `_HERMES_CORE_TOOLS` |
| `hermes_state.py` | `SessionDB` — SQLite session store with FTS5 search |
| `hermes_constants.py` | `get_hermes_home()`, `display_hermes_home()` — profile-aware paths |
| `hermes_cli/commands.py` | Central `COMMAND_REGISTRY` — single source of truth for all slash commands |

### Self-Registering Tools

Each `tools/*.py` file calls `registry.register()` at module load. `discover_builtin_tools()` in `tools/registry.py` imports all such files automatically — there is no manual import list. After registering, a tool is only *exposed to the agent* if its name appears in a toolset in `toolsets.py`.

Adding a core tool requires two files: `tools/your_tool.py` (with `registry.register()`) and a line in `toolsets.py`. For custom/local tools, use the plugin route (`~/.hermes/plugins/<name>/`) instead.

### Slash Commands

All slash commands are defined as `CommandDef` entries in `COMMAND_REGISTRY` (`hermes_cli/commands.py`). Everything downstream — CLI dispatch, gateway dispatch, Telegram menu, Slack routing, autocomplete, help text — derives from this registry automatically. Adding an alias requires only updating the `aliases` tuple on the existing `CommandDef`.

### TUI Process Model

```
hermes --tui
  └─ Node (Ink/React)  ──stdio JSON-RPC──  Python (tui_gateway/)
       renders UI                           AIAgent + tools + sessions
```

TypeScript owns the screen; Python owns sessions, model calls, and slash command logic. The dashboard (`hermes dashboard`) embeds the real `hermes --tui` via a PTY bridge — do not re-implement the chat surface in React.

### Config Loaders (three paths — use the right one)

| Loader | Used by |
|--------|---------|
| `load_cli_config()` in `cli.py` | Interactive CLI mode |
| `load_config()` in `hermes_cli/config.py` | `hermes tools`, `hermes setup`, CLI subcommands |
| Direct YAML load | Gateway runtime (`gateway/run.py`) |

Add new config keys to `DEFAULT_CONFIG` in `hermes_cli/config.py`. Secrets (API keys, tokens) go in `~/.hermes/.env` and must be listed in `OPTIONAL_ENV_VARS` in `hermes_cli/config.py`.

### Plugins

- **General plugins** (`hermes_cli/plugins.py`): register tools, lifecycle hooks (`pre/post_tool_call`, `pre/post_llm_call`, `on_session_start/end`), and CLI subcommands via a `register(ctx)` function. Discovery runs as a side effect of importing `model_tools.py`.
- **Memory providers** (`plugins/memory/<name>/`): implement `MemoryProvider` ABC from `agent/memory_provider.py`. **No new in-tree memory providers** — ship as standalone plugin repos instead.
- **Model providers** (`plugins/model-providers/<name>/`): separate discovery system, scanned lazily on first `get_provider_profile()` call.

Plugins MUST NOT modify core files (`run_agent.py`, `cli.py`, `gateway/run.py`, etc.).

## Critical Rules

### Paths — always use `get_hermes_home()`

```python
# GOOD
from hermes_constants import get_hermes_home
config_path = get_hermes_home() / "config.yaml"

# BAD — breaks profiles
config_path = Path.home() / ".hermes" / "config.yaml"
```

Use `display_hermes_home()` for any user-facing string that mentions the hermes home path.

### Prompt Caching

Do not alter past context, toolsets, memories, or system prompts mid-conversation. Cache-breaking dramatically increases costs. The only legitimate mid-conversation context change is context compression. Slash commands that mutate system-prompt state must default to deferred invalidation (next session), with an opt-in `--now` flag.

### Dependency Pinning

All `pyproject.toml` deps must be exact-pinned (`==X.Y.Z`) or range-bounded with a ceiling. Never add a bare `>=X.Y.Z` without an upper bound. After changing deps, run `uv lock` to regenerate `uv.lock`. This policy was introduced after supply-chain attacks (Mini Shai-Hulud worm, May 2026).

### Cross-Platform

Never assume Unix. Don't use `os.kill(pid, 0)` for liveness (use `psutil.pid_exists()`), `os.killpg`, hardcoded `/tmp` paths (use `tempfile.gettempdir()`), or POSIX-only signal constants. Don't introduce new `simple_term_menu` usage — use `hermes_cli/curses_ui.py` instead.

### Tests — no change-detector tests

Don't write tests that snapshot current data (model catalog entries, config version literals, enumeration counts). Write invariant tests (relationships between data, not specific values).

## Skills vs. Tools

The answer is almost always **skill**. A skill is a `SKILL.md` file in `skills/<category>/<name>/` with instructions, optional helper scripts, and frontmatter metadata. A new Python tool is only needed for capabilities requiring binary data, streaming, precise auth flows, or real-time events that can't go through the terminal.

Bundled skills (`skills/`) ship by default. Heavier/niche official skills go in `optional-skills/`. New memory providers must be standalone repos.

### Skill authoring requirements (enforced at review)

1. `description` ≤ 60 characters, one sentence, ends with a period — no marketing words.
2. Reference Hermes native tools by name (`` `terminal` ``, `` `read_file` ``, `` `web_extract` ``, etc.) — not raw shell utilities like `grep`, `cat`, `curl`.
3. Skills using POSIX-only primitives must declare `platforms: [linux]` or `[macos, linux]` in frontmatter.
4. Skill tests at `tests/skills/test_<skill>_skill.py` — stdlib + pytest + `unittest.mock` only, no live network calls.

## Adding a Core Tool

Built-in tools require exactly **two file changes**. For custom/niche tools, prefer the plugin route (`~/.hermes/plugins/<name>/`) over growing core.

**1. Create `tools/your_tool.py`:**
```python
import json
from tools.registry import registry

def check_requirements() -> bool:
    return bool(os.getenv("EXAMPLE_API_KEY"))

registry.register(
    name="example_tool",
    toolset="example",
    schema={"name": "example_tool", "description": "...", "parameters": {...}},
    handler=lambda args, **kw: json.dumps({"result": example_tool(args.get("param", ""), kw.get("task_id"))}),
    check_fn=check_requirements,
    requires_env=["EXAMPLE_API_KEY"],
)
```

**2. Add to a toolset in `toolsets.py`** — auto-discovery imports the file and registers the schema, but the tool is only exposed to agents when its name appears in a toolset.

Use `get_hermes_home()` for any state files; `display_hermes_home()` in schema descriptions. All handlers must return a JSON string.

## Profiles

Hermes supports fully isolated instances via `HERMES_HOME`. The core mechanism: `_apply_profile_override()` in `hermes_cli/main.py` sets `HERMES_HOME` before any module imports, so all `get_hermes_home()` calls automatically scope to the active profile.

Rules:
- Always `from hermes_constants import get_hermes_home` — never `Path.home() / ".hermes"`.
- Always `from hermes_constants import display_hermes_home` for user-facing path strings.
- Profile root (`_get_profiles_root()`) is always `Path.home() / ".hermes" / "profiles"` (HOME-anchored, not HERMES_HOME-anchored) so `hermes -p coder profile list` can see all profiles regardless of which is active.
- Tests that mock `Path.home()` must also `monkeypatch.setenv("HERMES_HOME", ...)` since code reads the env var, not `Path.home()`.

## Known Pitfalls

**Hardcoded `~/.hermes` paths break profiles.** Use `get_hermes_home()` / `display_hermes_home()` everywhere. This caused 5 bugs fixed in PR #3575.

**`simple_term_menu` has rendering bugs** (ghost duplication in tmux/iTerm2). Existing call sites in `hermes_cli/main.py` are legacy fallback only. New interactive menus must use `hermes_cli/curses_ui.py`.

**`\033[K` (ANSI erase-to-EOL) leaks as literal `?[K`** under `prompt_toolkit`'s `patch_stdout`. Use space-padding instead: `f"\r{line}{' ' * pad}"`.

**`_last_resolved_tool_names` is a process-global in `model_tools.py`.** `_run_single_child()` in `delegate_tool.py` saves and restores it around subagent execution. New code reading this global may see stale state during child agent runs.

**Cross-tool references in schema descriptions cause hallucinations.** Don't mention tools from other toolsets in schema text — they may be unavailable. Add cross-references dynamically in `get_tool_definitions()` in `model_tools.py` instead (see the `browser_navigate` / `execute_code` blocks for the pattern).

**The gateway has two sequential message guards** — both must bypass approval/control commands. The base adapter (`gateway/platforms/base.py`) queues messages when `session_key in self._active_sessions`; the runner (`gateway/run.py`) intercepts `/stop`, `/approve`, etc. New commands that must reach the runner while an agent is blocked must bypass BOTH guards and dispatch inline, not via `_process_message_background()`.

**Squash-merging a stale branch silently reverts recent fixes.** Ensure the branch is rebased onto `main` before squash-merging. Verify with `git diff HEAD~1..HEAD` — unexpected deletions are a red flag.

**Tests must not write to `~/.hermes/`.** The `_isolate_hermes_home` autouse fixture in `tests/conftest.py` redirects `HERMES_HOME` to a temp dir. When testing profile features, also mock `Path.home()`:
```python
@pytest.fixture
def profile_env(tmp_path, monkeypatch):
    home = tmp_path / ".hermes"
    home.mkdir()
    monkeypatch.setattr(Path, "home", lambda: tmp_path)
    monkeypatch.setenv("HERMES_HOME", str(home))
    return home
```

**Absence can be load-bearing.** Missing `__init__.py` files in `tests/` are intentional — adding them can make test directories importable as dotted packages that shadow real plugins, deleting their `register()` at import time.

---

> For the full development reference — contribution rubric, footprint ladder, AIAgent class API, detailed CLI/TUI/desktop architecture, plugin authoring, skills standards, delegation, cron, kanban, and skin system — see [AGENTS.md](AGENTS.md).
