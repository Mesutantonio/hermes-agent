# Agent O

Enterprise orchestration agent built on [Hermes Agent](https://github.com/NousResearch/hermes-agent) (Nous Research, MIT licence).

## What it is

Agent O is an AI orchestration layer that receives requests, reasons about what is needed, and either executes tasks directly using skills and tools, or delegates to existing agents and APIs as sub-agents. New capabilities are skills, not new agents.

Built for enterprise use with support for permission-scoped execution, human-in-the-loop approval workflows, immutable audit trails, and non-human triggers.

---

## Phases

### Phase 1 — Strip to the core (branch: `first-changes` → merged to `main`)

**Goal:** Start from upstream Hermes Agent and remove everything that is not relevant to enterprise orchestration. Leave the engine intact; cut the consumer surface.

**What was removed:**

| Removed | Reason |
|---|---|
| Electron desktop app (`apps/`) | No desktop client for enterprise deployment |
| React dashboard (`web/`) | Consumer UI; Agent O connects via REST API, not a browser dashboard |
| Ink/React TUI + Python backend (`ui-tui/`, `tui_gateway/`) | Full-screen terminal UI not needed; plain `cli.py` REPL is sufficient for dev testing |
| VS Code / Zed / JetBrains ACP integration (`acp_adapter/`, `acp_registry/`) | IDE plugins not relevant to enterprise agent deployment |
| Docusaurus docs site (`website/`) | Nous Research public docs; Agent O will maintain its own |
| Homebrew packaging (`packaging/`) | Agent O deploys via Docker, not Homebrew |
| Nix packaging (`nix/`, `flake.nix`, `flake.lock`) | Docker is the target; Nix not needed |
| Marketing and visual assets (`assets/`, `infographic/`) | Nous Research branding |
| Internal planning docs (`.plans/`, `plans/`) | Nous Research internal notes |
| Non-English READMEs (`README.ur-pk.md`, `README.zh-CN.md`) | Not maintained for Agent O |
| Open-source community files (`CONTRIBUTING.md`, `SECURITY.md`) | Nous Research community files; Agent O will define its own policies |
| GitHub CI/CD and issue templates (`.github/`) | Nous Research pipeline; Agent O will configure its own |
| Termux constraints (`constraints-termux.txt`) | Android terminal platform; not a target |
| direnv config (`.envrc`) | Auto-activated Nix flake and watched removed dirs |
| Git contributor map (`.mailmap`) | Nous Research contributor identity map |
| SWE-bench benchmark runner (`mini_swe_runner.py`) | Nous Research research tooling |
| Optional MCP catalog (`optional-mcps/`) | Nous-curated MCP configs; Agent O will configure its own |
| Telegram-specific files (`hermes_cli/telegram_managed_bot.py`, `gateway/assets/`) | Dead code after Telegram adapter removal |
| Telegram/stream plan (`docs/plans/2026-06-09-003-*.md`) | Telegram-specific fix plan |
| Dashboard profile wizard design (`docs/design/profile-builder.md`) | Depends on removed dashboard |
| All consumer messaging platform adapters (`gateway/platforms/telegram.py`, `slack.py`, `signal.py`, `matrix.py`, `email.py`, `sms.py`, `dingtalk.py`, `feishu*.py`, `weixin.py`, `wecom*.py`, `whatsapp*.py`, `bluebubbles.py`, `yuanbao*.py`, `qqbot/`) | 26 files — Agent O connects via REST API and webhooks, not messaging apps |
| Consumer platform plugins (`plugins/platforms/discord/`, `google_chat/`, `homeassistant/`, `irc/`, `line/`, `mattermost/`, `ntfy/`, `photon/`, `simplex/`) | 9 consumer platform plugins removed with their adapters |
| npm workspace root (`package.json`, `package-lock.json`) | Tied together `ui-tui/`, `apps/`, `web/` — all removed |
| Optional Nous skills library (`optional-skills/`) | Nous Research open-source skill library; Agent O defines its own |
| Contributor quick-start script (`setup-hermes.sh`) | Nous Research contributor tooling |
| Windows Docker Compose override (`docker-compose.windows.yml`) | Agent O targets Linux containers |

**What was kept and why:**

| What | Why |
|---|---|
| Core agent engine (`run_agent.py`, `model_tools.py`, `agent/`, `hermes_state.py`) | The ReAct loop, tool orchestration, session persistence — the foundation everything is built on |
| Tools system (`tools/`, `toolsets.py`) | Self-registering tool registry — Agent Y, Maximo API, Oracle API will be registered here |
| Skills system (`skills/`, `cron/`) | Workflow library and scheduler — Agent O learns and stores skills, runs scheduled jobs |
| Gateway framework (`gateway/`) | Full gateway kept; only consumer platform adapters removed. `api_server.py` (REST) and `webhook.py` (non-human triggers) are the primary connection surfaces |
| CLI and display (`cli.py`, `hermes_cli/`) | Developer terminal interface for local testing. Not exposed to enterprise users |
| Fine-tuning pipeline (`batch_runner.py`, `trajectory_compressor.py`) | Generates training trajectories from Agent O runs for LLM fine-tuning on tool-calling behaviour |
| Teams adapter (`plugins/platforms/teams/`) | Full Microsoft Teams adapter for planned future integration |
| Microsoft Graph webhook (`gateway/platforms/msgraph_webhook.py`) | Inbound Teams/Office365 event handler |
| Memory providers (`plugins/memory/`) | Kept as **reference implementations only** — patterns for building a future custom enterprise memory module with per-tenant isolation |
| Terminal backends (`tools/environments/`) | Kept pending decision on hosting model (see Phase 2) |
| Docs (`docs/`) | Kept: kanban spec, middleware contract, observability hooks, Docker network security, SSL RCA |
| Locales (`locales/`) | Gateway runtime dependency — `agent.i18n` loads these at startup |
| `LICENSE` | MIT licence — must be preserved |
| `MANIFEST.in`, `.hadolint.yaml` | Build and linting config still relevant |

**Upstream sync (end of Phase 1):**
Pulled 160 upstream commits from NousResearch/hermes-agent (to `f9c8d95e4`) into all kept files. Key changes picked up: `delegate_task(background=true)` async subagents, secrets redaction in debug logs, MCP `mcp__` prefix normalisation, memory skill-scaffolding strip, websockets core dep, Teams SDK as installable extra.

**Branch commits:**
```
3c6378aeb docs: add upstream sync policy and last-sync record to CLAUDE.md
3fb5deb98 chore: sync kept files with upstream hermes-agent (160 commits ahead)
0c6834cdb docs: track pending Phase 1 removals in CLAUDE.md
4f217eb56 docs: Phase 1 build log in AGENT_O.md, pointer in CLAUDE.md
94fe322d6 docs: add AGENT_O.md — initial Agent O readme stub
ddd6e6b68 docs(claude): add Explicitly Kept section and complete removed table
06f4abb8d chore: remove .envrc
2cbaf0579 chore: remove Telegram remnants and irrelevant design docs
4ef7cd2b2 chore: remove consumer gateway platform adapters and plugins
286d10398 chore: remove optional-mcps and mini_swe_runner.py
a30383ea9 docs(claude): complete the removed-files table with all cleanup to date
9207edfc9 docs(claude): document kept/removed file decisions for LICENSE, MANIFEST, hadolint, mailmap
fff25a875 chore: remove .mailmap
5ef0d55ee chore: remove open-source community docs, GitHub CI/CD, and Termux constraints
dd7395794 docs(claude): document Agent O cleanup — removed dirs and reasons
7288d2f33 chore: remove consumer UIs, IDE integration, docs site, and planning docs
78a6a53da chore: remove non-code assets, Nix support, and non-English READMEs
```

---

### Phase 2 — Baseline Docker image (branches: `second-changes` + `testing` → merged to `main`, complete)

**Goal:** Produce a clean, buildable, bootable Docker image of Agent O that responds to a real prompt via the REST API — portable enough to move into the company Azure repo.

**Decisions made:**

| Decision | Rationale |
|---|---|
| Agent O always runs in a Docker container | Simplifies the hosting model; no need for SSH/cloud execution backends |
| Terminal execution: `local` + `docker` only | `local` = commands run inside the Agent O container. `docker` kept pending DinD decision. SSH, Modal, Singularity, Daytona, Daytona, Managed Modal, file_sync all removed |
| Memory providers: kept as reference only | Not active. Personal-use providers don't support per-tenant isolation. Kept as integration patterns for the future enterprise memory module |
| Browser tool disabled in baseline | Its JS runtime (`agent-browser`) came from `package.json` which was removed with `ui-tui/`. Can be re-added later if needed |

**What was removed in Phase 2:**

| Removed | Reason |
|---|---|
| `tools/environments/ssh.py` | Remote SSH execution — not needed in Docker deployment |
| `tools/environments/modal.py`, `managed_modal.py`, `modal_utils.py` | Modal cloud sandboxes — external service, not used in self-hosted Docker |
| `tools/environments/singularity.py` | Apptainer/HPC containers — no HPC use case |
| `tools/environments/daytona.py` | Daytona cloud workspaces — external service |
| `tools/environments/file_sync.py` | File sync helper only used by the removed remote backends |
| `ui-tui/`, `tui_gateway/` | Pending from Phase 1 — executed in Phase 2 |
| `optional-skills/` | Pending from Phase 1 — executed in Phase 2 |
| `setup-hermes.sh`, `docker-compose.windows.yml` | Pending from Phase 1 — executed in Phase 2 |
| `package.json`, `package-lock.json` | Pending from Phase 1 — executed in Phase 2 |

**What was fixed:**

- `tools/terminal_tool.py` — module-level imports of deleted backends (`SingularityEnvironment`, `SSHEnvironment`, `ModalEnvironment`, `ManagedModalEnvironment`) caused an immediate `ModuleNotFoundError` on startup. Fixed by removing the deleted imports and adding a local `_get_scratch_dir()` helper backed by the kept `base.py:get_sandbox_dir()`.

**All steps complete. Baseline verified June 2026.**

**Testing results (branch: `testing`):**

| Stage | Test | Result |
|---|---|---|
| 1 | Import check — `discover_builtin_tools()` | PASS |
| 2 | One-shot smoke — OpenRouter `anthropic/claude-3-haiku` | PASS |
| 3 | Targeted test suite — tools/gateway/agent | PASS (failures only from removed components or macOS env) |
| 4 | Docker — build → run → `/health` → `/v1/chat/completions` | PASS |

**Additional fixes found during testing:**
- `agent/secret_scope.py`, `hermes_cli/managed_scope.py`, `tools/async_delegation.py`, `cron/scheduler_provider.py` — all missed in the original upstream sync; pulled and committed.
- Dead consumer platform entries (Mattermost, Signal, Weixin, BlueBubbles, QQBot, Yuanbao) removed from `hermes setup` menu — adapter files were deleted in Phase 1 but they were still appearing as selectable options, confusing new users.

**Sharing the baseline image:**
```bash
# Export (on your machine)
docker save agent-o:baseline | gzip > agent-o-baseline.tar.gz

# Import and run (supervisor's machine — Docker Desktop only required)
docker load < agent-o-baseline.tar.gz
docker run -d --name agent-o -v ~/.hermes:/opt/data agent-o:baseline gateway run
docker exec -it agent-o hermes setup
```

---

## What Agent O can do now

- **Orchestration engine** — full ReAct loop (Reason → Act → Observe), parallel tool execution, context compression, budget/iteration limits
- **33 active tools** — terminal, file, web, browser automation, code execution, vision, delegation (including async background subagents), kanban, cron, memory, skills, MCP, send message, clarify, approval
- **29 model providers** — Anthropic, OpenAI, OpenRouter, Gemini, Bedrock, Azure, DeepSeek, NVIDIA, Ollama, xAI, and more
- **Skills library** — research, software-development, devops, mlops, data-science, productivity, email, github, note-taking, autonomous-ai-agents
- **8 memory provider reference implementations** — honcho, mem0, supermemory, byterover, hindsight, holographic, openviking, retaindb
- **REST API gateway** — OpenAI-compatible `/v1/chat/completions`, `/v1/runs`, `/health` via `api_server.py` (port 8642)
- **Webhook gateway** — inbound HTTP webhooks with HMAC validation for non-human triggers
- **Scheduler** — cron/interval-based job runner across full agent pipeline
- **Sub-agent delegation** — isolated parallel sub-agents (`delegate_task(background=true)`)
- **Fine-tuning pipeline** — `batch_runner.py` + `trajectory_compressor.py` for training-data generation

**What it cannot do yet (Phase 3+):**
- Talk to Agent Y, IBM Maximo, or Oracle Procurement (custom tool integrations)
- Receive messages from the company platform (company messaging adapter)
- Enforce permission scopes per user/tenant
- Produce an immutable audit trail
- Pause for human approval and resume hours later (HITL)

---

## Licence

Built on Hermes Agent — MIT licence. See [LICENSE](LICENSE).
