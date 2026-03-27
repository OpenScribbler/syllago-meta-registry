# Contributing to syllago-meta-registry

This is a syllago registry. Follow the conventions below to add content.

## Directory Structure

### Universal content (works with any AI tool)

```
skills/<name>/
    SKILL.md          # Required. Frontmatter (name, description) + instructions.
    .syllago.yaml     # Optional metadata (tags, version, author).
    README.md         # Optional. Human-readable docs.

agents/<name>/
    AGENT.md          # Required. Frontmatter + agent specification.
    .syllago.yaml     # Optional metadata.
    README.md         # Optional.

mcp/<name>/
    README.md         # Required. Describes the MCP server configuration.
    .syllago.yaml     # Optional metadata.
```

### Provider-specific content

```
rules/<provider>/<name>/
    rule.md           # Required. The rule content.
    README.md         # Required. Human-readable description.
    .syllago.yaml     # Optional metadata.

hooks/<provider>/<name>/
    hook.json         # Required. Hook configuration.
    README.md         # Required.
    .syllago.yaml     # Optional metadata.

commands/<provider>/<name>/
    command.md        # Required. Command definition.
    README.md         # Required.
    .syllago.yaml     # Optional metadata.
```

Supported provider slugs: `claude-code`, `cursor`, `copilot`, `windsurf`, `zed`, `aider`, `continue`, `gemini-cli`, `amp`.

## Naming Conventions

- Use lowercase letters, numbers, hyphens, and underscores only.
- No spaces, dots, or special characters.
- Examples: `code-review`, `test_helper`, `my-rule-v2`

## registry.yaml

The `registry.yaml` at the root describes this registry:

```yaml
name: syllago-meta-registry
description: A short description of this registry.
version: 1.0.0
```

Bump `version` when you make significant changes.

## Adding Content

To contribute, create a new directory under the appropriate content type folder following the structure above. Each piece of content needs at minimum its required file (e.g., `SKILL.md` for skills, `rule.md` for rules) and optionally a `.syllago.yaml` for metadata.
