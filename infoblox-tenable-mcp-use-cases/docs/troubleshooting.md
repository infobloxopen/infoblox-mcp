# Troubleshooting

## Infoblox MCP Server

| Symptom | Resolution |
|---|---|
| `401 Unauthorized` | The key is invalid or expired, or the header format is wrong. It must be exactly `Authorization: Token <key>`, with a space after `Token`. Regenerate the key under CSP ▸ User Access ▸ Service API Keys if needed. |
| `403 Forbidden` on a call | The Service User's role doesn't allow that call. Check the User Group, Access Policy and Role assignment. |
| Connection refused or timeout | Confirm the URL is `https://csp.infoblox.com/mcp` and that your network can reach `csp.infoblox.com` on port 443. |
| Threat Defense calls return 403 or 404 while DDI calls work | Threat Defense endpoints need an active Threat Defense subscription. DDI tools still work without it. |
| Tool not found or unknown tool name | The tool may have been renamed in a server update. Refresh the tool list (`/mcp` in Claude Code, or restart Claude Desktop). |
| `mcp-remote` changes the Token header (Claude Desktop) | Pass the header through the `AUTH_HEADER` environment variable and reference it as `Authorization:${AUTH_HEADER}`. Don't write `Token <key>` directly in args. |
| OAuth browser prompt appears (Claude Desktop) | Add `--auth-timeout 5` to the `mcp-remote` args. |
| `mcp-remote` fails with a Node error | Install Node.js 20 or later and check with `node --version`. |

## Tenable Hexa AI MCP Server

See [Tenable's documentation](https://docs.tenable.com/exposure-management/Content/getting-started/hexa-AI-MCP.htm) for authentication and connection errors.

## Claude Code

| Symptom | Resolution |
|---|---|
| A server is missing or shows as failed in `/mcp` | Make sure the environment variables were exported in the same shell before you started `claude` (`set -a; source .env; set +a`). |
| Slash commands don't appear | Start `claude` from the repository root so it picks up `.claude/commands/`. |
| The model uses only one server | Name both in the prompt, for example "Use the infoblox server for DNS/DHCP and the tenable server for findings." |

## Results

| Symptom | Resolution |
|---|---|
| The model can't resolve the subnet name | Give the CIDR directly, or check the subnet's name in Infoblox IPAM. |
| Results differ between runs | This is expected with AI models. Fix the output format by listing the columns you want, and ask the model to state which data source it counted from. |
| Few or no MAC matches | Check that DHCP fixed-address reservations exist for the range. Dynamic leases may not have a stable MAC-to-IP mapping. |
