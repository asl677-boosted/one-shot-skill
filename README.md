# one-shot-skill
Claude Code to Design Skill — full-stack design-to-deployment pipeline from PRD to production in one shot.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/asl677-boosted/one-shot-skill/main/SKILL.md \
  -o ~/.claude/skills/one-shot && \
mkdir -p ~/.claude/skills/one-shot-refs && \
curl -fsSL https://raw.githubusercontent.com/asl677-boosted/one-shot-skill/main/references/phase-details.md \
  -o ~/.claude/skills/one-shot-refs/phase-details.md
```

Then invoke with `/one-shot` in Claude Code.

## What it does

Seven-phase pipeline:
1. Install design skills
2. Connect shadcn MCP
3. Update CLAUDE.md + config
4. Pull & analyze PRD (Notion or Linear)
5. Invoke skills (min viable set)
6. Design execution (Figma or Pencil)
7. Deploy to Vercel + branch protection
