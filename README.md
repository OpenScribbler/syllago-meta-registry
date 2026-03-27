# syllago-meta-registry

The official syllago meta-registry — skills, agents, loadouts, and content for syllago usage and management.

This registry contains content that helps users work with syllago itself: skills for common syllago workflows, agents for automated content management, loadouts for quick-start configurations, and provider-specific rules and hooks.

## Using this registry

```sh
syllago registry add <git-url>
syllago registry sync
```

Then browse and install content:

```sh
syllago list
syllago install <content-name>
```

## Structure

- `skills/` -- Skills for syllago workflows
- `agents/` -- Agents for content management
- `mcp/` -- MCP server configurations
- `rules/` -- Provider-specific rules
- `hooks/` -- Provider-specific hooks
- `commands/` -- Provider-specific commands
- `loadouts/` -- Curated content bundles
