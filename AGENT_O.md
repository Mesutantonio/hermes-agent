# Agent O

Enterprise orchestration agent built on [Hermes Agent](https://github.com/NousResearch/hermes-agent) (Nous Research, MIT licence).

## What it is

Agent O is an AI orchestration layer that receives requests, reasons about what is needed, and either executes tasks directly using skills and tools, or delegates to existing agents and APIs as sub-agents. New capabilities are skills, not new agents.

Built for enterprise use with support for permission-scoped execution, human-in-the-loop approval workflows, immutable audit trails, and non-human triggers.

## Build log

### Phase 1 — Foundation (June 2026)

Started from the upstream Hermes Agent codebase (synced to `main` as of June 2026) and stripped everything that is not relevant to enterprise orchestration.

**Removed:**
- All consumer-facing UIs — Electron desktop app, React dashboard, Ink/React TUI, VS Code/Zed/JetBrains IDE integration
- All consumer messaging platform adapters — Telegram, Slack, WhatsApp, Signal, Matrix, Discord, email, SMS, DingTalk, Feishu, WeCom, Weixin, YuanBao, BlueBubbles, QQBot, and 9 corresponding platform plugins
- Nous Research open-source community files — `CONTRIBUTING.md`, `SECURITY.md`, GitHub Actions/CI, issue templates, PR templates
- Nix packaging, Homebrew formula, Termux constraints, `.envrc`
- Nous Research internal docs, planning files, marketing assets, non-English READMEs
- SWE-bench benchmark runner (`mini_swe_runner.py`)
- Optional MCP catalog (`optional-mcps/`)
- Telegram-specific remnants — bot management helper, BotFather screenshot, stream overflow plan

**Kept and why:**

| What | Why |
|---|---|
| Core agent engine (`run_agent.py`, `model_tools.py`, `agent/`, `hermes_state.py`) | The ReAct loop, tool orchestration, session persistence, and agent internals are the foundation Agent O is built on |
| Tools system (`tools/`, `toolsets.py`) | Self-registering tool registry — Agent Y, Maximo API, Oracle API will be registered here |
| Skills system (`skills/`, `cron/`) | Workflow library and scheduler — Agent O learns and stores skills, runs scheduled jobs |
| Gateway framework (`gateway/`) | Full gateway kept; only the consumer platform adapters removed. `api_server.py` (REST) and `webhook.py` (non-human triggers) are Agent O's primary connection surfaces |
| CLI and display (`cli.py`, `hermes_cli/`) | Developer terminal interface for local testing. Not exposed to enterprise users |
| Fine-tuning pipeline (`batch_runner.py`, `trajectory_compressor.py`) | Generates training trajectories from Agent O runs for LLM fine-tuning on enterprise tool-calling behaviour |
| Teams adapter (`plugins/platforms/teams/`) | Full Microsoft Teams adapter kept for planned future integration |
| Microsoft Graph webhook (`gateway/platforms/msgraph_webhook.py`) | Inbound Teams/Office365 event handler, paired with the Teams adapter |
| Memory providers (`plugins/memory/`) | Kept as reference implementations pending design of the enterprise memory system |
| Terminal backends (`tools/environments/`) | Kept pending decision on company hosting model |
| Docs (`docs/`) | Kept: kanban spec, middleware contract, observability hooks, Docker network security, SSL RCA. Removed: Telegram plan, dashboard design proposal |

**What's next (Phase 2):**
- Register Agent Y, IBM Maximo API, and Oracle Procurement API as tools
- Write the company platform adapter (extending `BasePlatformAdapter`)
- Build out enterprise skills for the three target use cases
- Design permission-scoped execution and audit trail

## Licence

Built on Hermes Agent — MIT licence. See [LICENSE](LICENSE).
