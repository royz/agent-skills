# Agent Skills

A collection of AI coding agent skills that can be easily installed into your local agent configurations.

## Installation

You can install these skills using the `skills` CLI.

### 1. Install all skills

To install all available skills from this repository, run:

```bash
npx skills add https://github.com/royz/agent-skills
```

### 2. Install a specific skill

If you want to install a specific skill instead of all of them, use the `--skill` flag followed by the name of the skill.

```bash
npx skills add https://github.com/royz/agent-skills --skill <skill-name>
```

**Example:**
To install the `mantine-init` skill:
```bash
npx skills add https://github.com/royz/agent-skills --skill mantine-init
```

## Available Skills

- **mantine-init**: Integrates the Mantine component library into a React project.
