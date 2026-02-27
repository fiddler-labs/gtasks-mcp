# Google Tasks MCP Server

An MCP server that lets AI coding assistants (OpenCode, Claude, Cursor, etc.) read and manage your Google Tasks. Forked from [@zcaceres/gtasks-mcp](https://github.com/zcaceres/gtasks-mcp).

## Quick Start

```bash
git clone https://github.com/fiddler-labs/gtasks-mcp.git
cd gtasks-mcp
npm install
npm run build
```

## Google Cloud Setup (one-time)

You need OAuth credentials so the server can access your Google Tasks.

1. Go to [Google Cloud Console](https://console.cloud.google.com/projectcreate) and create a new project (or use an existing one)
2. [Enable the Google Tasks API](https://console.cloud.google.com/workspace-api/products) for your project
3. [Configure an OAuth consent screen](https://console.cloud.google.com/apis/credentials/consent) — "External" is fine, add yourself as a test user
4. Add the scope `https://www.googleapis.com/auth/tasks`
5. [Create an OAuth Client ID](https://console.cloud.google.com/apis/credentials/oauthclient) — select **"Desktop App"** as the application type
6. Download the JSON file of your client's OAuth keys
7. Save it as `gcp-oauth.keys.json` in the root of this repo

## Authenticate

Run the auth flow to get a credentials token:

```bash
npm start auth
```

This opens a browser for Google sign-in. After authorizing, credentials are saved to `.gtasks-server-credentials.json` (gitignored). You only need to do this once — tokens auto-refresh.

Verify it works:

```bash
npm start
```

You should see no errors. Press Ctrl+C to stop.

## OpenCode Integration

Add to your OpenCode config (`~/.config/opencode/opencode.json`):

```json
{
  "mcp": {
    "gtasks": {
      "type": "local",
      "command": ["node", "/absolute/path/to/gtasks-mcp/dist/index.js"],
      "enabled": true
    }
  }
}
```

Replace `/absolute/path/to/gtasks-mcp` with the actual path where you cloned the repo.

Restart OpenCode. You'll now have tools like `gtasks_list`, `gtasks_create`, `gtasks_complete`, etc.

### Custom slash command (optional)

Create `~/.config/opencode/commands/tasks.md`:

```markdown
---
description: Show my Google Tasks
---
Use the gtasks list_tasklists tool to get all my task lists, then use the gtasks list tool to show all incomplete tasks grouped by list.

Format the output clearly showing:
- Each list as a section header
- Each task with its title, due date (if set), and notes (if set)

Keep it concise — just the task titles unless there's a due date or notes worth showing.
```

Then type `/tasks` in OpenCode to see your task list.

## Claude Desktop / Cursor / Other MCP Clients

Add to your MCP client config:

```json
{
  "mcpServers": {
    "gtasks": {
      "command": "node",
      "args": ["/absolute/path/to/gtasks-mcp/dist/index.js"]
    }
  }
}
```

## Available Tools

| Tool | Description |
|------|-------------|
| `list_tasklists` | List all task lists with IDs |
| `create_tasklist` | Create a new task list |
| `update_tasklist` | Rename a task list |
| `delete_tasklist` | Delete a task list and all its tasks |
| `list` | List tasks, optionally filtered by list and/or status |
| `create` | Create a task (with optional notes, due date, parent) |
| `update` | Update a task's title, notes, status, or due date |
| `complete` | Mark a task as completed |
| `delete` | Delete a task permanently |
| `move` | Reorder a task or make it a subtask |
| `search` | Search tasks by title or notes |
| `clear` | Clear all completed tasks from a list |

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `GTASKS_OAUTH_KEYS_PATH` | Path to OAuth client keys JSON | `./gcp-oauth.keys.json` |
| `GTASKS_CREDENTIALS_PATH` | Path to saved credentials | `./.gtasks-server-credentials.json` |

## License

MIT — see [LICENSE](./LICENSE). Original work by [Zach Caceres](https://github.com/zcaceres).
