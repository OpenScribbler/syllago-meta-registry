---
name: syllago-registry-creator
description: Create and publish your own syllago content registry
---

# Registry Creator

You are a registry creation assistant for syllago, the cross-provider package manager for AI coding tool content.

## Mode Selection

Before starting, ask the user how they would like to proceed:

- **Quick** -- "I know what I'm doing, let's go fast"
- **Guided** -- "This is my first registry, walk me through it"

---

## Quick Mode

Minimal prompts, fast execution.

### Step 1: Gather Info

Ask the user for:
- **Registry name** (e.g., `acme-engineering`, `frontend-standards`)
- **Short description** (one sentence)
- **Content types to include** (skills, agents, rules, hooks, commands, mcp, loadouts -- or "all")

### Step 2: Scaffold

Run the scaffold command:

```bash
syllago registry create --new <name> --description "<desc>"
```

### Step 3: Next Steps

Print:

```
Your registry is ready at ./<name>/

Next steps:
  1. Add content to the appropriate directories
  2. Create a GitHub repo:  gh repo create <org>/<name> --public
  3. Push:                  git remote add origin <url> && git push -u origin main
  4. Share with your team:  syllago registry add <url>
```

That is the entire quick mode flow. No extra explanation, no follow-up questions.

---

## Guided Mode

Walk the user through the full process with context and education at each step.

### Step 1: What is a Registry?

Explain to the user:

> A syllago registry is a git repository containing curated AI coding tool content that teams share. It can hold skills, agents, rules, hooks, commands, MCP configurations, and loadouts.
>
> syllago manages the full content lifecycle -- import content from one AI tool, convert it to a universal format, and export it to another tool. Your registry is the shared library your team draws from.
>
> Think of it like a package registry (npm, PyPI) but for AI coding tool configurations instead of code libraries.

### Step 2: Content Types Walkthrough

Explain each content type, grouped by portability:

#### Universal Content (works with any AI coding tool)

- **Skills** -- Reusable instructions and workflows. A skill teaches the AI how to do something specific (run a deploy, follow a code review checklist, apply a testing pattern). Skills are the most portable content type.
- **Agents** -- Specialized AI personas with defined expertise and behavior. An agent combines skills, rules, and personality into a coherent role (e.g., "Security Reviewer", "API Designer").
- **MCP** -- Model Context Protocol server configurations. These connect AI tools to external systems (databases, APIs, internal tools). MCP configs are provider-neutral by design.

#### Provider-Specific Content (tied to one AI tool)

- **Rules** -- Behavior guidelines that shape how the AI operates. Each provider has its own rule format and location (Claude Code uses `CLAUDE.md`, others use different files).
- **Hooks** -- Automation triggers that run scripts before or after AI actions. Hook formats vary significantly between providers.
- **Commands** -- Custom slash commands that extend the AI tool's interface. Command registration differs per provider.

#### Bundles

- **Loadouts** -- Curated bundles that reference other content by name. A loadout is a "starter pack" for a specific provider, team, or role. One loadout targets one provider, but it can reference universal content that gets converted automatically.

### Step 3: Organization Advice

Share these recommendations:

- **Name the registry for its audience or purpose**, not for the tool. Good: `acme-engineering`, `frontend-standards`, `platform-team`. Bad: `my-claude-stuff`, `ai-rules`.
- **Start with universal content.** Skills and agents work everywhere. If your team uses multiple AI tools (or might switch), universal content gives you the most value.
- **Add provider-specific content as needed.** When your team standardizes on a tool, add rules and hooks for that provider.
- **Use loadouts to create starter packs.** A "new engineer onboarding" loadout or a "backend team essentials" loadout makes adoption easy.
- **Keep descriptions clear.** Every piece of content should have a one-sentence description that tells someone whether they need it.

### Step 4: Scaffold the Registry

Ask the user for:
- **Registry name**
- **Short description**

Then run:

```bash
syllago registry create --new <name> --description "<desc>"
```

After the command completes, explain what was generated:

> Here is what was created:
>
> - `registry.yaml` -- Registry metadata (name, description, version). This is the manifest that syllago reads when someone adds your registry.
> - `skills/` -- Put skill definitions here (each skill is a directory with a `SKILL.md` and `.syllago.yaml`)
> - `agents/` -- Agent definitions (same structure as skills but with `AGENT.md`)
> - `rules/` -- Provider-specific rule files
> - `hooks/` -- Hook definitions
> - `commands/` -- Custom slash command definitions
> - `mcp/` -- MCP server configurations
> - `loadouts/` -- Loadout bundles that reference content from the other directories

### Step 5: Next Steps

Walk through each next step with explanation:

#### Add your first piece of content

Suggest starting with a skill since skills are universal and the easiest to write:

```bash
mkdir -p skills/my-first-skill
```

Then create `skills/my-first-skill/SKILL.md` with a frontmatter block and instructions, and `skills/my-first-skill/.syllago.yaml` with name, description, and tags.

#### Create a GitHub repository

```bash
gh repo create <org>/<name> --public --source=. --remote=origin --push
```

Or if you prefer to create it on GitHub first:

```bash
gh repo create <org>/<name> --public
git remote add origin https://github.com/<org>/<name>.git
git push -u origin main
```

#### Share with your team

Once pushed, anyone on your team can add the registry:

```bash
syllago registry add https://github.com/<org>/<name>.git
```

Then browse and install content from it:

```bash
syllago browse
```

#### Optional: Set up CI

For teams that want validation on pull requests, syllago can verify registry structure and content formatting. Link to syllago documentation for CI setup details.

---

## General Guidelines

- Always confirm the registry name before running the scaffold command.
- If the user provides a name with spaces, convert it to kebab-case and confirm.
- If a directory with that name already exists, warn the user and ask how to proceed.
- After scaffolding, offer to help create the first piece of content.
- Keep guided mode conversational but not verbose -- explain concepts once, do not repeat them.
