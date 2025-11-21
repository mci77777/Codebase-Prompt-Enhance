# Codebase MCP Monorepo

A modular MCP (Model Context Protocol) server ecosystem for codebase retrieval and prompt enhancement, built as a monorepo with three independent packages.

## 📦 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     User Applications                        │
├──────────────────────────┬──────────────────────────────────┤
│  Prompt Enhance Desktop  │  MCP Client (Claude/GPT)         │
│  (Standalone exe)        │  (stdio transport)               │
└──────────┬───────────────┴──────────────┬───────────────────┘
           │                               │
           ▼                               ▼
┌──────────────────────────┐   ┌──────────────────────────────┐
│ @codebase-mcp/           │   │ @codebase-mcp/               │
│ prompt-enhance           │   │ retrieval                    │
│                          │   │                              │
│ - Express Web Server     │   │ - MCP Server (stdio)         │
│ - EnhancePromptService   │   │ - IndexManager               │
│ - Web UI (templates)     │   │ - codebaseRetrievalTool      │
│ - Prompt Editor          │   │ - Web Management UI          │
└──────────┬───────────────┘   └──────────┬───────────────────┘
           │                               │
           └───────────┬───────────────────┘
                       ▼
            ┌──────────────────────────┐
            │ @codebase-mcp/shared     │
            │                          │
            │ - Config (settings.toml) │
            │ - Logger (file + WS)     │
            │ - PathUtils (WSL)        │
            │ - FileScanner (.gitignore)│
            │ - Encoding Detection     │
            └──────────────────────────┘
```

## 🎯 Packages

### [@codebase-mcp/shared](packages/shared/)
Core utilities shared across all modules:
- Configuration management (TOML)
- Logging system with file rotation and WebSocket broadcasting
- Cross-platform path utilities (Windows, WSL, Unix)
- File scanning with .gitignore support
- Multi-encoding detection (UTF-8, GBK, GB2312, Latin-1)

### [@codebase-mcp/prompt-enhance](packages/prompt-enhance/)
Standalone desktop application for prompt enhancement:
- Web-based prompt editor
- AI-powered prompt optimization
- Project context integration
- Packagable as Windows exe (via pkg)

### [@codebase-mcp/retrieval](packages/retrieval/)
MCP server for semantic codebase search:
- Incremental indexing with SHA-256 deduplication
- Semantic search via MCP protocol
- Web management interface
- Real-time log broadcasting

## 🚀 Quick Start

### For End Users

**Prompt Enhance (Desktop App)**
```bash
# Download the latest release
# https://github.com/your-repo/releases

# Run the exe (Windows)
prompt-enhance-win-x64.exe

# Or via npm
npx @codebase-mcp/prompt-enhance
```

**Codebase Retrieval (MCP Server)**
```bash
# Via npx
npx @codebase-mcp/retrieval

# Or install globally
npm install -g @codebase-mcp/retrieval
codebase-retrieval
```

### For Developers

```bash
# Clone the repository
git clone https://github.com/your-repo/codebase-mcp.git
cd codebase-mcp

# Install dependencies (uses npm workspaces)
npm install

# Build all packages
npm run build

# Or build specific packages
npm run build:shared
npm run build:prompt-enhance
npm run build:retrieval
```

## 🛠️ Development

### Monorepo Structure

```
codebase-mcp-monorepo/
├── packages/
│   ├── shared/              # Core utilities
│   ├── prompt-enhance/      # Desktop app
│   └── retrieval/           # MCP server
├── package.json             # Root workspace config
├── tsconfig.base.json       # Shared TypeScript config
└── .github/workflows/       # CI/CD automation
```

### Development Commands

```bash
# Development mode with hot reload
npm run dev:prompt-enhance
npm run dev:retrieval

# Build all packages
npm run build

# Build specific package
npm run build:shared
npm run build:prompt-enhance
npm run build:retrieval

# Run tests
npm test
npm run test:retrieval

# Clean build artifacts
npm run clean

# Package Prompt Enhance as exe
npm run package:prompt-enhance
```

### TypeScript Project References

This monorepo uses TypeScript project references for efficient builds:
- Each package has its own `tsconfig.json`
- Shared base configuration in `tsconfig.base.json`
- Automatic dependency resolution via `references` field

### Adding Dependencies

```bash
# Add to specific package
npm install <package> -w @codebase-mcp/shared
npm install <package> -w @codebase-mcp/prompt-enhance
npm install <package> -w @codebase-mcp/retrieval

# Add to root (dev dependencies)
npm install <package> -D
```

## ⚙️ Configuration

All modules share a unified configuration file:

**Location**: `~/.codebase-mcp/settings.toml`

**Example**:
```toml
# Codebase Retrieval API
BASE_URL = "https://d6.api.augmentcode.com/"
TOKEN = "your-token-here"

# Prompt Enhance API
ENHANCE_BASE_URL = "https://api.x.ai"
ENHANCE_TOKEN = "your-xai-token-here"
MODEL = "grok-2-1212"

# Web Server
WEB_PORT = 8090

# Indexing
BATCH_SIZE = 10
MAX_LINES_PER_BLOB = 800

# File Extensions
TEXT_EXTENSIONS = [".py", ".js", ".ts", ".jsx", ".tsx", ".java", ".go", ".rs"]

# Exclude Patterns
EXCLUDE_PATTERNS = [".venv", "node_modules", ".git", "__pycache__", "dist"]
```

Configuration is auto-generated on first run with sensible defaults.

## 🔧 MCP Client Setup

### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "codebase-retrieval": {
      "command": "npx",
      "args": ["@codebase-mcp/retrieval"]
    }
  }
}
```