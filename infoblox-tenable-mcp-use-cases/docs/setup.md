# Setup

This guide connects an MCP host to both the Infoblox MCP Server and the Tenable Hexa AI MCP Server. Claude Code is the primary example. Claude Desktop is covered at the end.

## 1. Infoblox: Create a Service User and Service API Key

A Service API key has no permission selector of its own. It inherits whatever access its Service User has. Create the Service User with the right role first, then issue the key.

### 1a. Create a Service User with a read-only role

You need a CSP role with User Management and Service API Key permissions, such as Administrator or Access Control Administrator.

1. Sign in to the Infoblox Portal at [csp.infoblox.com](https://csp.infoblox.com).
2. Go to System ▸ Administration ▸ User Access, then open the Users tab.
3. Click Create Service User and give it a descriptive name, for example `svc-tenable-mcp-readonly`.
4. Add the Service User to a User Group whose Access Policy grants a read-only DDI role. Create the group, policy or role first if it doesn't exist.
5. Ensure that the Service User is part of the ib-mcp-server-user or ib-mcp-server-admin groups.

> [!IMPORTANT]
> The Infoblox MCP Server's write tools run immediately against your tenant, with no dry-run mode. Every use case in this repository is read-only, so use a read-only role.

### 1b. Generate the Service API key

1. Go to User Access ▸ Service API Keys and click Create Service API Key.
2. Name: for example `Tenable-MCP-Use-Cases`.
3. Service User: select the user from step 1a. This choice sets the key's permissions.
4. Expires at: a 90-day expiry is recommended.
5. Access View: leave as All unless you need to restrict the key to one DNS view.
6. Click Save, and copy the key right away. It's shown only once.

Store the key in a secrets manager or in your local `.env` file. Never commit it.

## 2. Tenable: Get Hexa AI MCP Server Access

Follow [Tenable's Hexa AI MCP Server documentation](https://docs.tenable.com/exposure-management/Content/getting-started/hexa-AI-MCP.htm) to get:

* the MCP server URL
* the credential or authentication method your Tenable tenant uses

Use an account or key that has read access to the assets and findings you want to query.

## 3. Configure Your Credentials

```bash
cp config/.env.example .env
```

Edit `.env`:

```bash
INFOBLOX_API_KEY=<your Infoblox Service API key>
TENABLE_MCP_URL=<Tenable Hexa AI MCP Server URL>
TENABLE_MCP_AUTH_HEADER=<header value required by Tenable>
```

## 4. Register Both Servers in Claude Code

### Option A: Project config (recommended for this repo)

```bash
cp config/mcp.json.example .mcp.json
set -a; source .env; set +a
claude
```

Claude Code reads `.mcp.json` from the project root and expands `${VAR}` references from your shell environment. Both `.mcp.json` and `.env` are ignored by Git.

If Tenable uses an interactive sign-in flow instead of a static header, remove the `headers` block from the `tenable` entry. Then authenticate from the `/mcp` menu in Claude Code.

### Option B: Register with the CLI

Run these commands in a regular terminal, not inside a Claude session:

```bash
claude mcp add --transport http infoblox https://csp.infoblox.com/mcp \
  --header "Authorization: Token $INFOBLOX_API_KEY"

claude mcp add --transport http tenable "$TENABLE_MCP_URL" \
  --header "Authorization: $TENABLE_MCP_AUTH_HEADER"
```

Add `--scope user` to make a server available in all your projects.

## 5. Verify the Connections

Start `claude` and run:

```text
/mcp
```

Both `infoblox` and `tenable` should show as connected. Then run quick read-only checks:

```text
Using the infoblox server, call list_all_available_services and tell me how many services are returned.
```

```text
Using the infoblox server, make a GET request to Ipamsvc /ipam/ip_space and list the IP space names.
```

A healthy Infoblox connection returns the catalog of CSP services, such as IPAM, DNS, DHCP and Threat Defense. An empty IP space list still means you have read access; it just means no IP spaces exist yet. A `403 Forbidden` error means the key is missing DDI read permissions.

For Tenable, ask a simple question that only Tenable can answer:

```text
Using the tenable server, how many assets are in 10.20.30.0/24?
```

Replace the CIDR with a range in your environment.

## Claude Desktop (Alternative)

Claude Desktop needs the `mcp-remote` proxy, which requires Node.js 20 or later, to reach remote HTTP servers. Use [`config/claude_desktop_config.example.json`](../config/claude_desktop_config.example.json) as a starting point and merge it into your `claude_desktop_config.json`.

Keep these points in mind for the Infoblox entry:

* Pass the header through the `AUTH_HEADER` environment variable and reference it as `Authorization:${AUTH_HEADER}`. If you put `Token <key>` directly in the args, `mcp-remote` strips the space.
* `--auth-timeout 5` stops `mcp-remote` from starting a browser OAuth flow, because the server uses a static token.
* Restart Claude Desktop after editing the config.

Configure the `tenable` entry according to Tenable's documentation.

## Next Steps

* Run a use case: [use-cases/](../use-cases/)
* Learn how the prompts are built: [prompting-tips.md](prompting-tips.md)
* If something doesn't work: [troubleshooting.md](troubleshooting.md)
