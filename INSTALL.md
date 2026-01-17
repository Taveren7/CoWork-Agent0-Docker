# Installation & Configuration Guide

Complete guide for setting up CoWork Agent0 Docker on macOS, Windows, and Linux.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [First-Run Setup](#first-run-setup)
4. [Customizing Folder Access](#customizing-folder-access)
5. [Windows-Specific Setup](#windows-specific-setup)
6. [Customizing Agent Behavior](#customizing-agent-behavior)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Required Software
- **Docker Desktop** - [Download here](https://www.docker.com/products/docker-desktop/)
- **Git** - [Download here](https://git-scm.com/downloads)

### API Keys (at least one)
- **OpenAI** - [Get API key](https://platform.openai.com/api-keys)
- **Anthropic** - [Get API key](https://console.anthropic.com/)
- **Google AI** - [Get API key](https://makersuite.google.com/app/apikey)

---

## Installation

### macOS / Linux

```bash
# 1. Clone the repository
git clone https://github.com/Taveren7/CoWork-Agent0-Docker.git
cd CoWork-Agent0-Docker

# 2. Start Docker Desktop (if not running)

# 3. Launch the container
docker-compose up -d

# 4. Verify it's running
docker ps

# 5. Open the web UI
open http://localhost:50001
```

### Windows (PowerShell)

```powershell
# 1. Clone the repository
git clone https://github.com/Taveren7/CoWork-Agent0-Docker.git
cd CoWork-Agent0-Docker

# 2. Start Docker Desktop (if not running)

# 3. Launch the container
docker-compose up -d

# 4. Verify it's running
docker ps

# 5. Open the web UI
Start-Process "http://localhost:50001"
```

---

## First-Run Setup

### Step 1: Open Settings
1. Navigate to **http://localhost:50001**
2. Click the **⚙️ Settings** gear icon (top right)

### Step 2: Configure Your LLM Model

#### For OpenAI (Recommended)
| Setting | Value |
|---------|-------|
| **Chat Model Provider** | `openai` |
| **Chat Model Name** | `gpt-5.2` (or `gpt-4o`, `gpt-4-turbo`) |
| **Temperature** | `1` |
| **API Key** | Your OpenAI API key |

#### For Anthropic Claude
| Setting | Value |
|---------|-------|
| **Chat Model Provider** | `anthropic` |
| **Chat Model Name** | `claude-3-5-sonnet-20241022` |
| **Temperature** | `1` |
| **API Key** | Your Anthropic API key |

### Step 3: Select the Cowork Agent
1. Go to **Settings → Agent**
2. Select **`cowork`** from the dropdown
3. Click **Save**

### Step 4: Start Using It
1. Click **New Chat**
2. Try: *"What folders can you access?"*
3. Or: *"List the files on my desktop"*

---

## Customizing Folder Access

### Default Mounts
The default `docker-compose.yml` includes:

```yaml
volumes:
  - ./agent_workspace:/work_dir          # Read-write workspace
  - ~/Desktop:/desktop:ro                # Read-only
  - ~/Documents:/documents:ro            # Read-only
```

### Adding More Folders

Edit `docker-compose.yml` and uncomment or add volumes:

```yaml
volumes:
  # Core workspace
  - ./agent_workspace:/work_dir
  
  # User folders (read-only)
  - ~/Desktop:/desktop:ro
  - ~/Documents:/documents:ro
  - ~/Downloads:/downloads:ro
  
  # Project folders (read-write for active work)
  - ~/Projects:/projects
  - ~/Code:/code
  
  # Media folders (read-only)
  - ~/Pictures:/pictures:ro
  - ~/Movies:/movies:ro
  - ~/Music:/music:ro
```

### After Changing Volumes

```bash
# Restart the container
docker-compose down && docker-compose up -d
```

### Update the Agent Prompt

After adding new folders, update `agents/cowork/prompts/agent.system.main.role.md`:

```markdown
### Accessible Folders
- `/work_dir` - Your main workspace (read-write)
- `/desktop` - User's Desktop (read-only)
- `/documents` - User's Documents (read-only)
- `/downloads` - User's Downloads (read-only)     # ← Add new folders
- `/projects` - User's Projects (read-write)
```

---

## Windows-Specific Setup

### Path Differences

Windows uses different home directory paths. Edit `docker-compose.yml`:

**Change FROM (macOS):**
```yaml
- ~/Desktop:/desktop:ro
- ~/Documents:/documents:ro
```

**Change TO (Windows):**
```yaml
- C:/Users/YourUsername/Desktop:/desktop:ro
- C:/Users/YourUsername/Documents:/documents:ro
```

Or use the Windows environment variable:
```yaml
- ${USERPROFILE}/Desktop:/desktop:ro
- ${USERPROFILE}/Documents:/documents:ro
```

### Full Windows docker-compose.yml Example

```yaml
services:
  agent0:
    image: agent0ai/agent-zero
    container_name: agent0
    tty: true
    stdin_open: true

    ports:
      - "50001:80"

    volumes:
      # Core workspace
      - ./agent_workspace:/work_dir

      # Windows user folders (adjust YourUsername)
      - C:/Users/YourUsername/Desktop:/desktop:ro
      - C:/Users/YourUsername/Documents:/documents:ro
      
      # OR use environment variable
      # - ${USERPROFILE}/Desktop:/desktop:ro
      # - ${USERPROFILE}/Documents:/documents:ro

      # Agent configuration
      - ./agents/cowork:/a0/agents/cowork
      - ./tmp/settings.json:/a0/tmp/settings.json
```

### Windows Commands

```powershell
# Start the container
docker-compose up -d

# Stop the container
docker-compose down

# View logs
docker logs agent0 -f

# Restart
docker-compose down; docker-compose up -d
```

---

## Customizing Agent Behavior

### Editing the Agent Prompt

The agent's behavior is defined in:
```
agents/cowork/prompts/agent.system.main.role.md
```

You can modify:
- **Safety rules** - What the agent can/cannot do
- **Workflow** - PLAN → EXECUTE → VERIFY → SUMMARIZE
- **Output format** - What the agent produces at the end

### Example: Make Agent More Verbose

Add to the prompt:
```markdown
### Communication Style
- Explain your reasoning step-by-step
- Show your work before presenting final results
- Ask for confirmation before large operations
```

### Example: Add Custom Tools

Create new files in `agents/cowork/`:
```
agents/cowork/
├── prompts/
│   └── agent.system.main.role.md
└── tools/
    └── my_custom_tool.py
```

---

## Troubleshooting

### Container Won't Start

```bash
# Check for errors
docker logs agent0

# Check if port 50001 is in use
lsof -i :50001  # macOS/Linux
netstat -ano | findstr 50001  # Windows
```

### "Empty compose file" Error

Make sure `docker-compose.yml` is not empty and has no syntax errors:
```bash
docker-compose config
```

### Can't See Desktop/Documents

1. Check Docker Desktop has file sharing permissions
2. On Mac: System Preferences → Security → Privacy → Full Disk Access → Docker
3. On Windows: Docker Desktop → Settings → Resources → File Sharing

### Agent Not Using Cowork Profile

1. Open Settings → Agent
2. Select `cowork`
3. Click Save
4. Start a **new chat** (existing chats keep old settings)

### Changes Not Taking Effect

```bash
# Fully restart the container
docker-compose down && docker-compose up -d
```

---

## Useful Commands

| Command | Description |
|---------|-------------|
| `docker-compose up -d` | Start in background |
| `docker-compose down` | Stop container |
| `docker-compose logs -f` | Follow logs |
| `docker exec -it agent0 bash` | Shell into container |
| `docker-compose restart` | Restart container |

---

## Support

- **Agent Zero Docs**: https://github.com/frdel/agent-zero
- **Issues**: https://github.com/Taveren7/CoWork-Agent0-Docker/issues
