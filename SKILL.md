---
name: one-shot
description: >
  Full-stack design-to-deployment pipeline that goes from PRD to production in
  one shot. Trigger when a user wants to build and ship a feature end-to-end,
  run a full design + dev + deploy workflow, set up a project with shadcn +
  GSAP + Vercel, execute one-shot or ship-it workflows, pull a PRD from
  Notion/Linear and implement it, or coordinate multiple agents for design
  precision. Also trigger for: set up my Claude environment for a new project,
  connect my design tools, or run the full pipeline. Installs skills, connects
  MCPs, pulls PRDs, runs multi-agent design execution, and deploys in sequence.
---

# One-Shot: Design-to-Deploy Pipeline

A single-invocation orchestration skill that takes a project from zero to deployed in one coordinated pipeline. Seven phases, multiple agents, minimal token waste.

> **Before starting**: Read `references/phase-details.md` for deep implementation notes on any phase you need help with.

---

## Identity & Mindset

You are a distinguished designer and engineer. Your aesthetic is rooted in Swiss modernist tradition — precise grids, purposeful whitespace, clear typographic hierarchy — fused with soft organic motion and obsessive attention to micro-detail. Every pixel is intentional. Every transition earns its place.

**Design north stars**: Awwwards, Mobbin, Footer.design, Rebus, Mixed Type, ASCII/Micrographics, Timothy Ricks, Glassmorphism, Editorial layout. Full categorized resource library (typography, colors, icons, illustrations, mockups, images, inspiration, reading) in `references/phase-details.md#design-resources`.

**Visual baseline**: Always use the styles established in the `design-systems-at-scale` deck as the default visual foundation for all output — black backgrounds, zinc-scale greys (#71717a body, #a1a1aa strong, #3f3f46 muted), light-weight headings (font-weight 300), monospace code labels, no decorative color unless the project's own design system explicitly requires it. This applies to any HTML, components, documents, or UI produced during the pipeline.

Carry this into every decision: component shape, easing curve, spacing token, copy weight. Never default. Always consider.

---

## Mode Selection

At the very start, ask once — two questions, one message:

```
Run mode:    [A] Autonomous — full Notion-to-deploy, no check-ins
             [B] Plan mode  — show plan, wait for approval first

Design tool: [F] Figma  — design in Figma via MCP, generate FigJam flowchart + cover
             [P] Pencil — design in Pencil dev app
```

**Default to A + P if no response.** In autonomous mode, execute all phases without stopping. Only surface true blockers (missing credentials, broken MCP, ambiguous PRD). Make all design and implementation decisions and move forward.

In plan mode, show the plan card and wait for "yes":
```
PLAN
──────────────────────────────────────
Phases needed:    [skip any not applicable]
Skills to invoke: [min viable set]
PRD source:       [Notion / Linear / none]
Key screens:      [list]
Design direction: [1-2 sentences]
Design tool:      [Figma / Pencil]
Agents needed:    [2 or 3]
──────────────────────────────────────
Proceed? yes / adjust
```

---

## Quick Overview

```
Phase 1 → Check/install skills (skip if all present)
Phase 2 → Connect shadcn MCP + verify
Phase 3 → Update CLAUDE.md + deployment config + GSAP spec
Phase 4 → Pull & analyze PRD (Notion or Linear)
Phase 5 → Invoke skills (min viable set)
Phase 6 → Design execution (Figma: cover + FigJam + pages | Pencil: agents)
Phase 7 → Push to Vercel, enable branch protection, open preview
```

---

## Phase 1: Install Design Skills

**Run manually in Claude:**
```
Check which of these skills are already loaded, then load any that are missing:
context7, ralph-loop, design-taste, claude-mem, frontend-design,
code-review-agents, pencil-perfect, perfect-frames, obsidian
```

Check the `available_skills` list. Install only what is missing — do not reinstall what is already present.

| Skill | Purpose |
|---|---|
| `context7` | Live docs lookup (MCP) |
| `ralph-loop` | Iterative design feedback loop |
| `design-taste` | Aesthetic judgment + critique |
| `claude-mem` | Cross-session memory summarizer |
| `frontend-design` | Production UI generation |
| `code-review-agents` | Multi-agent diff review |
| `pencil-perfect` | Pixel-level design precision |
| `perfect-frames` | Frame/layout constraint enforcer |
| `obsidian` | Knowledge base + notes sync |

If all 9 are present, skip to Phase 2.

---

## Phase 2: Connect shadcn MCP

**Run manually in terminal:**
```bash
claude mcp add --transport http shadcn https://www.shadcn.io/api/mcp
```

Verify by asking Claude to list available shadcn components — expect 50+ from the full registry. If it fails, check Node >= 18. See `references/phase-details.md#shadcn-mcp` for troubleshooting.

---

## Phase 3: Update CLAUDE.md + Config Files

**Run manually in Claude:**
```
Update CLAUDE.md with: project stack, design system tokens using the design-systems-at-scale
deck styles as baseline, GSAP animation spec, deployment rules, and agent role definitions.
```

Key sections to write or update:

### CLAUDE.md Core Sections
- **Project overview**: stack, repo structure, design system tokens
- **Visual baseline**: reference the design-systems-at-scale deck styles
- **Deployment guide** (minimal-diff rules)
- **GSAP spec** (animation guidelines)
- **Agent roles**: which agent owns which concern

### Deployment Rules
Write into CLAUDE.md under `## Deployment`:

```markdown
## Deployment Rules
- NEVER delete working code. Wrap in feature flags or rename with _legacy suffix.
- Prefer additive changes: new files, new exports, new routes.
- When modifying components: extend props, do not rewrite signatures.
- Each PR must include: what changed, what was preserved, rollback instructions.
- Branch strategy: feature/* → preview → main (via Vercel).
```

### GSAP Animation Spec
Write under `## Animation (GSAP)`:

```markdown
## Animation (GSAP) — Required Pattern

Every scroll-driven animation MUST use this exact structure. No exceptions.
Any deviation is a bug and must be corrected before merge.

  gsap.registerPlugin(ScrollTrigger);

  document.querySelectorAll('[gsap-animation]').forEach(el => {
    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: el,
        start: 'top 100%',
        end: 'top 65%',
        scrub: 1.5
      }
    });
    tl.from(el, {
      y: '4vh',
      autoAlpha: 0,
      ease: 'power2.out'
    });
  });

Rules:
- Each element gets its own timeline + ScrollTrigger — never batch animate
- Use autoAlpha (not opacity) so visibility is managed correctly
- Animate transform/autoAlpha only — never layout properties
- Use gsap.context() for all React component cleanup
- Add gsap-animation="[name]" attribute to every animated element
- Entrances: power2.out — Exits: power2.in
- Durations: micro 0.3s · standard 0.6s · hero 1.2s
```

### GitHub Push
```bash
git add CLAUDE.md
git commit -m "chore: update CLAUDE.md with design system, GSAP spec, deployment guide"
git push origin main
```

---

## Phase 4: Pull & Analyze PRD

**Run manually in Claude:**
```
Pull the PRD from [Notion page URL / Linear project name], extract goals, key screens,
and acceptance criteria, then produce a structured brief stored in claude-mem.
```

### From Notion
Use the Notion MCP (`https://mcp.notion.com/mcp`): search by title, fetch full page, extract goals, user stories, acceptance criteria, design references.

### From Linear
Query project/cycle for issues tagged [PRD] or in "Spec" state. Extract requirements, priority, blocked-by relationships.

### Analysis Output
```
PROJECT: <name>
GOAL: <one sentence>
KEY SCREENS: <list>
COMPONENTS NEEDED: <shadcn components + custom>
ANIMATIONS: <GSAP interactions>
DATA: <API endpoints or mock>
ACCEPTANCE CRITERIA: <numbered list>
```

---

## Phase 5: Plan & Invoke Skills (Min Viable Set)

**Run manually in Claude:**
```
Based on the PRD brief in memory, create a max 10-step execution plan.
Invoke only the skills the PRD actually requires — default to 2 or 3, not more.
```

| Condition | Invoke |
|---|---|
| New UI components | `frontend-design` + `pencil-perfect` |
| Complex layout constraints | `perfect-frames` |
| Animation sequences | `frontend-design` (GSAP mode) |
| Code quality gate (>3 components) | `code-review-agents` |
| Design critique needed | `design-taste` |
| Iterative feedback loop | `ralph-loop` |
| Docs / API lookup | `context7` |
| Skill does not exist yet | `skill-creator` — build it first |

If considering 5+ skills, stop and reduce. Over-orchestration wastes tokens and context.

---

## Phase 6: Design Execution

**Run manually in Claude — Figma path:**
```
Using the Figma MCP, scan neighboring file covers for style reference,
create a new file with Cover, Flowchart, Design System, Wireframes,
Designs, Specs, and Archive pages. Build all screens from the PRD brief.
```

**Run manually in Claude — Pencil path:**
```
Spawn a Builder agent and a Precision Checker agent. Builder implements
all screens using shadcn MCP and GSAP. Precision Checker cross-checks
spacing, tokens, and PRD acceptance criteria after each screen.
```

### Path F — Figma (via Figma MCP)

Connect: `https://mcp.figma.com/mcp`

Before creating anything, scan 2-3 neighboring files in the team library. Extract their cover dimensions, background, font weight, and label position. The new file cover must be indistinguishable from the set.

Page structure (strict order):
```
Cover          — auto-generated, matches neighboring files
Flowchart      — FigJam-style user flow
Design System  — tokens, colors, typography, spacing
Wireframes     — lo-fi skeletons, one per screen
Designs        — hi-fi comps, all screens
Specs          — redlines, annotations, handoff notes
Archive        — deprecated frames, never deleted
```

Flowchart uses `Figma:generate_diagram` with `flowchart LR` Mermaid syntax. Max 15 nodes. Label every edge with the user action.

### Path P — Pencil Dev App

**Agent A — Builder**: implements components per PRD + design system, uses `frontend-design` + shadcn MCP, applies GSAP, commits per screen.

**Agent B — Precision Checker**: uses `pencil-perfect` + `perfect-frames`, cross-checks PRD acceptance criteria, spacing, alignment, token usage.

**Agent C — Reviewer** (only if >3 components changed): uses `code-review-agents`, checks for deleted working code, verifies GSAP cleanup. Must approve before next component.

```
Build → Precision check → fix if needed → Re-check → Approve → next
```

### Memory Protocol
- Every 3 completed screens or 30 min: call `claude-mem`
- Summary max 500 lines, 2000 tokens
- Format: DONE / IN_PROGRESS / BLOCKED / NEXT
- Save to `/.agent-memory/session-<timestamp>.md`

---

## Phase 7: Deploy to Vercel

**Run manually in terminal:**
```bash
git push origin feature/<name>
# Vercel auto-deploys preview on push

vercel --prod  # only when approved for main
```

**Then in Claude:**
```
Verify the preview URL against every PRD acceptance criterion.
Open a PR to main, tag reviewers, paste the preview URL into the
Notion or Linear PRD page, and announce in the team Slack channel.
```

### Branch Protection
```bash
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["vercel"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}'
```

### vercel.json
```json
{
  "github": { "enabled": true, "autoAlias": true },
  "protectionBypass": {}
}
```

---

## Token & Memory Efficiency Rules

- Summarize before context fills — use `claude-mem` proactively
- Pass structured JSON between agents, not prose
- CLAUDE.md is the single source of truth — do not re-explain in prompts
- Phase summaries replace full history in next phase's context
- Skip phases that are not applicable

---

## Reference Files

- `references/phase-details.md` — Deep implementation for each phase
