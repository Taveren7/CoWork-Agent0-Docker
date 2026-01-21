# CoWork Agent0 Docker

> **Transform Agent Zero into an autonomous "Cowork" assistant** — like Claude's Cowork mode, but running locally in Docker with full file system access.

![Agent Zero](https://img.shields.io/badge/Agent%20Zero-v0.9.7-blue)
![Docker](https://img.shields.io/badge/Docker-Required-blue)
![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Windows%20%7C%20Linux-green)

## What is This?

This project configures [Agent Zero](https://github.com/frdel/agent-zero) to run as an **autonomous operations coworker** that can:

- 📁 **Read your Desktop & Documents** (read-only for safety)
- ✏️ **Create and edit files** in a sandboxed workspace
- 🔄 **Follow a structured workflow**: PLAN → EXECUTE → VERIFY → SUMMARIZE
- 🛡️ **Built-in safety guardrails**: No accidental deletions, versioned outputs

## Differences from Original Agent Zero

This repository (`CoWork-Agent0-Docker`) is a **custom configuration wrapper** around the original [agent-zero](https://github.com/frdel/agent-zero), not a modified fork of the core application code.

| Feature | Original Agent Zero | CoWork-Agent0-Docker |
|---------|---------------------|----------------------|
| **Deployment** | Python/Conda (CLI) | **Docker-First** (Sandboxed) |
| **File Access** | Full system access | **Read-Only** Desktop/Docs + Writable Workspace |
| **Persona** | General Purpose | **"Coworker"** (Strict PLAN → EXECUTE → VERIFY) |
| **Safety** | User responsibility | **Guardrails** (No delete, versioned outputs) |
| **OS Support** | Manual setup | **Unified** (macOS, Windows, Linux) |

**Key Benefit**: You get the full power of Agent Zero, but in a safe, sandboxed container that acts like a helpful coworker without risking your personal files.

## Quick Start

### Prerequisites
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- An OpenAI API key (or other LLM provider)

### Installation (macOS)

```bash
# Clone the repository
git clone https://github.com/Taveren7/CoWork-Agent0-Docker.git
cd CoWork-Agent0-Docker

# Start the container
docker-compose up -d

# Open the web UI
open http://localhost:50001
```

### First-Time Setup

1. Open **http://localhost:50001** in your browser
2. Click the **⚙️ Settings** gear icon
3. Configure your LLM:
   - **Provider**: `openai`
   - **Model**: `gpt-5.2` (or `gpt-4o`, `gpt-4-turbo`)
   - **Temperature**: `1`
   - Add your **API Key**
4. Go to **Settings → Agent** and select **`cowork`**
5. Click **Save** and start a new chat!

## Folder Structure

```
CoWork-Agent0-Docker/
├── docker-compose.yml          # Docker configuration
├── config.yaml                 # Reference config (not used by Agent Zero directly)
├── agent_workspace/            # Your sandboxed workspace (read-write)
│   ├── inbox/                  # Drop files here for processing
│   ├── working/                # Agent's scratch space
│   ├── output/                 # Final deliverables
│   ├── memory/                 # Prompts and persistent notes
│   └── logs/                   # Run logs
├── agents/cowork/              # Custom Cowork agent prompts
└── tmp/settings.json           # Agent Zero settings
```

## How It Works

| Your Mac Path | Container Path | Access |
|---------------|----------------|--------|
| `./agent_workspace/` | `/work_dir/` | Read-Write |
| `~/Desktop/` | `/desktop/` | Read-Only |
| `~/Documents/` | `/documents/` | Read-Only |

**Drop files in `agent_workspace/inbox/`** → Agent Zero sees them at `/work_dir/inbox/`

## Customization

See **[INSTALL.md](INSTALL.md)** for:
- Adding more folders (Downloads, Projects, etc.)
- Windows-specific setup
- Customizing the agent behavior
- Troubleshooting

## Safety Features

The Cowork agent includes built-in guardrails:
- ❌ Cannot delete files (unless explicitly told)
- ❌ Cannot modify Desktop/Documents (read-only)
- ✅ Creates versioned outputs (`report-v1.xlsx`, `report-v2.xlsx`)
- ✅ Always summarizes what it did

## Commands

```bash
# Start
docker-compose up -d

# Stop
docker-compose down

# View logs
docker logs agent0 -f

# Restart with changes
docker-compose down && docker-compose up -d
```

## License

MIT License - Feel free to modify and share!

## Credits

- [Agent Zero](https://github.com/frdel/agent-zero) - The amazing AI agent framework
- Inspired by Claude's Cowork mode
