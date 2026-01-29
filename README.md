# n8n Workflow Builder

A workspace template for designing, building, and optimizing high-quality n8n workflows with AI assistance. This project combines an MCP server with expert skills to give Claude deep knowledge of n8n's 1,084+ nodes and 2,700+ workflow templates.

## Features

- **MCP Server Integration** - Access to comprehensive n8n node documentation, properties, and operations
- **7 Expert Skills** - Specialized knowledge for expressions, patterns, validation, and code
- **Workflow Management** - Create, validate, and deploy workflows directly to your n8n instance
- **Template Library** - Search and deploy from 2,700+ workflow templates
- **Validation** - Catch errors before deployment with multiple validation profiles

## Project Structure

```
n8n-builder/
├── CLAUDE.md                    # Operating guide for Claude
├── .mcp/
│   └── n8n-mcp/                 # MCP server (submodule)
└── .skills/
    └── n8n-skills/              # Expert skills (submodule)
        └── skills/
            ├── n8n-expression-syntax/      # Expression patterns ({{}} syntax)
            ├── n8n-mcp-tools-expert/       # Tool usage mastery
            ├── n8n-workflow-patterns/      # Architectural patterns
            ├── n8n-validation-expert/      # Error interpretation
            ├── n8n-node-configuration/     # Node setup guidance
            ├── n8n-code-javascript/        # JavaScript in Code nodes
            └── n8n-code-python/            # Python in Code nodes
```

## Prerequisites

- [Claude Code](https://github.com/anthropics/claude-code) or Claude Desktop
- [Node.js](https://nodejs.org/) v18+ (for the MCP server)
- Optional: n8n instance with API access (for workflow management)

## Setup

### 1. Clone the Repository

```bash
git clone --recurse-submodules https://github.com/YOUR_USERNAME/n8n-builder.git
cd n8n-builder
```

If you already cloned without submodules:

```bash
git submodule update --init --recursive
```

### 2. Configure the MCP Server

Add to your Claude Desktop or Claude Code MCP configuration:

**Basic (documentation only):**
```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error"
      }
    }
  }
}
```

**Full (with n8n workflow management):**
```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "N8N_API_URL": "https://your-n8n-instance.com/api/v1",
        "N8N_API_KEY": "your-api-key"
      }
    }
  }
}
```

### 3. Alternative: Use Hosted Service

Skip local setup with the hosted MCP service:

1. Sign up at [dashboard.n8n-mcp.com](https://dashboard.n8n-mcp.com)
2. Get your API key
3. Connect your MCP client

Free tier includes 100 tool calls/day.

## Usage

Open the project in Claude Code or reference the CLAUDE.md file in Claude Desktop. Claude will automatically use the MCP tools and skills to help you:

### Example Prompts

**Create a workflow:**
> "Build a workflow that triggers on a webhook, validates the incoming JSON, sends a Slack notification, and stores the data in Airtable"

**Find the right nodes:**
> "What nodes can I use to integrate with Google Sheets?"

**Debug a workflow:**
> "Why is my HTTP Request node returning a 401 error?"

**Optimize an existing workflow:**
> "Review this workflow and suggest improvements for error handling"

### Key MCP Tools

| Tool | Purpose |
|------|---------|
| `search_nodes` | Find nodes by keyword |
| `get_node` | Get detailed node documentation |
| `validate_workflow` | Check workflow for errors |
| `search_templates` | Find proven workflow patterns |
| `n8n_create_workflow` | Create workflow in n8n |
| `n8n_update_partial_workflow` | Modify existing workflow |

## Workflow Design Principles

The CLAUDE.md file contains detailed guidelines, but key principles include:

1. **Start with the right trigger** - Webhook for integrations, Schedule for time-based, Manual for on-demand
2. **Validate data early** - Use IF nodes and Set nodes to check and normalize data
3. **Handle errors comprehensively** - Enable retries, add error triggers, log failures
4. **Design for maintainability** - Descriptive names, under 15 nodes, use sub-workflows
5. **Optimize for performance** - Batch large datasets, rate limit API calls

## Components

### n8n-MCP Server

The [n8n-mcp](https://github.com/czlonkowski/n8n-mcp) submodule provides:
- Documentation for 1,084 n8n nodes (537 core + 547 community)
- 2,646 pre-extracted configuration examples
- 2,709 workflow templates
- Workflow CRUD operations via n8n API
- Multiple validation profiles

### n8n Skills

The [n8n-skills](https://github.com/czlonkowski/n8n-skills) submodule provides specialized knowledge:

| Skill | Use Case |
|-------|----------|
| Expression Syntax | Writing `{{ }}` expressions correctly |
| MCP Tools Expert | Choosing the right tool for the task |
| Workflow Patterns | Designing robust architectures |
| Validation Expert | Understanding and fixing errors |
| Node Configuration | Setting up specific operations |
| Code JavaScript | Writing Code node scripts in JS |
| Code Python | Writing Code node scripts in Python |

## Safety Warning

**Never edit production workflows directly with AI.** Always:
- Make a copy of your workflow first
- Test in a development environment
- Export backups of important workflows
- Validate changes before deploying

## License

MIT

## Credits

- [n8n-mcp](https://github.com/czlonkowski/n8n-mcp) by [@czlonkowski](https://github.com/czlonkowski)
- [n8n-skills](https://github.com/czlonkowski/n8n-skills) by [@czlonkowski](https://github.com/czlonkowski)
- [n8n](https://n8n.io/) workflow automation platform
