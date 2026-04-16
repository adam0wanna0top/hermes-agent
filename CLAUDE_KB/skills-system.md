# Skills System

## Skill Definition
Skills are Markdown files with YAML frontmatter in `skills/` directory:
```markdown
---
name: my-skill
description: Does something useful
platforms: [cli, telegram, discord]
prerequisites: [exa]
required_environment_variables: [API_KEY]
setup: pip install something
metadata:
  category: productivity
---

# Skill Instructions
Detailed instructions the agent follows when this skill is loaded...
```

## Skill Lifecycle
1. **Bundle** — Skills live in `skills/` and `optional-skills/` directories
2. **Sync** — `tools/skills_sync.py` copies to `~/.hermes/skills/` using v2 manifest with MD5 three-way merge
3. **List** — `skills_list` tool returns metadata (name, description, category)
4. **View** — `skill_view` tool returns full content (progressive disclosure)
5. **Hub** — `skills_hub` tools search/install from GitHub, skills.sh, ClawHub, LobeHub
6. **Manage** — `skill_manager_tool` provides CRUD operations
7. **Guard** — `skills_guard.py` security-scans external skills (80+ threat patterns)

## Progressive Disclosure
- Tier 1: `skills_list()` — name, description, category (in system prompt)
- Tier 2: `skill_view()` — full SKILL.md content (loaded on demand)
- Tier 3: References, templates, scripts (loaded from skill subdirectory)

## Skills Hub (`tools/skills_hub.py`, 112KB)
External skill discovery from multiple sources:
- GitHub repositories
- skills.sh registry
- ClawHub
- LobeHub
- Custom sources

Search → Preview → Install → Security scan → Activate

## Security Scanning (`tools/skills_guard.py`)
80+ regex patterns across 10+ categories:
- Prompt injection detection
- Data exfiltration detection
- Unauthorized code execution
- Network access attempts
- File system manipulation
- Privilege escalation patterns

## Bundled Skills (27 categories in `skills/`)
apple, autonomous-ai-agents, creative, data-science, devops, email, gaming, github, media, mlops, mcp, productivity, research, smart-home, software-development, and more.

14 additional categories in `optional-skills/`.

## Skill in System Prompt
The prompt builder (`agent/prompt_builder.py`) includes:
1. Skills index: name + one-line description for all active skills
2. Full skill content: loaded when agent invokes `skill_view()`
3. Skill references/templates: loaded on demand from skill subdirectory
