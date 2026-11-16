# PeerContext for Codex

Read and contribute to your team's shared Markdown memory in Codex. The plugin connects Codex to the PeerContext daemon on your computer and includes the Shared Memory skill.

You need a local Codex installation with plugin marketplace support, Node.js 22.22 or later, and npm. Run Codex and PeerContext as the same user.

## First-time setup

Install the PeerContext CLI and start the foreground daemon in a terminal. Leave it running while you use PeerContext.

```sh
npm install -g peercontext@0.6.0-alpha.8
peercontext start
```

In Windows PowerShell, use `npm.cmd install -g peercontext@0.6.0-alpha.8` and `peercontext.cmd start`. Use the `.cmd` suffix for the other PeerContext and npm commands below as well.

In a second terminal, run `peercontext open`. Sign in in the local dashboard, then create a team or open an invitation from a trusted teammate. For an existing team, follow its device approval flow. Enter email codes, invitations, tokens, and keys only in the local dashboard or CLI. Keep them out of chat.

Use the marketplace commands below for Codex. `peercontext setup` also installs a standalone Codex integration and would give Codex a second copy of the memory tools.

If npm's default global prefix isn't writable, try `npm install -g --prefix "$HOME/.local" peercontext@0.6.0-alpha.8` on macOS or Linux and add `$HOME/.local/bin` to PATH. On Windows, use `npm.cmd install -g --prefix "$env:LOCALAPPDATA\PeerContext" peercontext@0.6.0-alpha.8` and add that prefix to your user Path. With any custom prefix, make the npm command directory visible to Codex as well as your terminal. That directory is `<prefix>/bin` on macOS and Linux and `<prefix>` on Windows. Check the default prefix with `npm prefix -g`.

### Optional macOS background service

If you want the daemon to run automatically when you sign in, stop the foreground daemon with Ctrl+C and register the owned per-user service:

```sh
peercontext service install
launchctl bootstrap "gui/$(id -u)" "$HOME/Library/LaunchAgents/com.shared-memory.agents.plist"
peercontext status
```

If an existing PeerContext service is already running, keep using it.

## Install the Codex plugin

```sh
codex plugin marketplace add kpourdeilami/peercontext-codex
codex plugin add peercontext@peercontext
```

After any migration below, restart Codex and start a new session to load the tools and skill. Open your project in Codex and bind it to your active PeerContext team:

```sh
peercontext init --workspace "/absolute/path/to/project"
```

When invoking memory tools that take a workspace, pass the project's absolute path. Codex starts the bundled MCP server in the plugin directory.

## Migrate an existing standalone Codex integration

If you used `peercontext setup` or `peercontext install-agent codex`, install the plugin first. Preview removal of the standalone MCP entry and skill using the same user and Codex home as the original installation:

```sh
peercontext install-agent codex --uninstall --dry-run
```

Review the listed actions. If they match the old PeerContext integration, run `peercontext install-agent codex --uninstall`. The CLI removes owned files and the owned MCP entry. If it reports a changed file or configuration, review those changes before removing any custom configuration. The migration keeps your daemon, team, project binding, and other agents' integrations. Restart Codex and use the plugin in a new session.

## Update or remove

Refresh the marketplace, install the plugin version it lists, then restart Codex:

```sh
codex plugin marketplace upgrade peercontext
codex plugin add peercontext@peercontext
```

Update the CLI with the npm install command and prefix used originally. Restart the foreground daemon or, for the macOS service, run `launchctl kickstart -k "gui/$(id -u)/com.shared-memory.agents"`.

To remove the Codex plugin and marketplace:

```sh
codex plugin remove peercontext@peercontext
codex plugin marketplace remove peercontext
```

This removes the Codex integration. Your PeerContext CLI, daemon, and team data stay on this computer.
