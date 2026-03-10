# Phase Details Reference

Deep implementation notes for each phase of the one-shot pipeline.

---

## shadcn MCP Troubleshooting {#shadcn-mcp}

**URL**: `https://ui.shadcn.com/docs/mcp`

### Setup Methods

**Method 1: npx (recommended)**
```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx",
      "args": ["-y", "shadcn@canary", "mcp"]
    }
  }
}
```

**Method 2: Claude Desktop config path**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

**Verification query**: Ask MCP: "List all available shadcn components" — should return 50+ components.

**Common failures**:
- Node <18: upgrade Node
- `shadcn@canary` not resolving: try `shadcn@latest`
- Config not reloaded: restart Claude Desktop after editing config

### What shadcn MCP gives you
- Component registry lookup
- Auto-generate `npx shadcn add <component>` commands
- Variant and prop documentation inline
- Theming token suggestions

---

## CLAUDE.md Full Template {#claude-md-template}

```markdown
# Project: <Project Name>

## Design Inspiration & Resources
Reference these when making decisions — not to copy, but to calibrate taste and find the right raw material. Swiss grid logic beneath all of it: alignment, proportion, reduction.

### Visual North Stars
Awwwards (interaction quality, layout ambition), Mobbin (mobile UI patterns, real-world flows), Footer.design (typographic restraint, negative space), Rebus (editorial boldness, type-as-image), Mixed Type (expressive typography, letter spacing), ASCII / Micrographics (texture through symbol, monospace beauty), Timothy Ricks (Webflow motion, scroll choreography), Glassmorphism (frosted surfaces, blur depth — use sparingly), Editorial (magazine layout, pull quotes, column rhythm).

### Typography
Sourcing and reference for typeface decisions:
Google Fonts, Adobe Fonts, MyFonts, Font Squirrel, Awwwards Free Fonts, Cufon Fonts, Nootype, Fonts in Use (see how fonts are used in context), DaFonts.

### Colors
Palette building and color harmony tools:
Coolors, Paletton, Khroma (AI-assisted), Picu Color, Adobe Colors, Muzli Color, Color Hunt, Design Seeds, Site Palette.

### Images & Photography
Free and premium asset sources:
Unsplash, Pexels, Pixabay, Freepik, Burst (Shopify), Kaboompics, Stocksnap.io, Remove.bg (background removal), Compressjpeg (optimization).

### Icons
Icon library selection by style and weight:
Hugeicons, Feather Icons, Iconly, Flaticon, Icons8, Font Awesome, Fontello, Iconoir, Freepik.

### Illustrations
Vector and character illustration sets:
unDraw, Humaaans, Ouch!, Blush, DrawKit, Whoosh, Is.graphics, Vecteezy, Freepik.

### Mockups
Device and context presentation:
Ulmock, Previewed, Smart Mockups, Unblast, Pixeden, Mockups-design, Canva, Behance, Freepik.

### Inspiration Browsing
Where to look when direction needs calibrating:
Dribbble, Behance, Awwwards, Mobbin, Pinterest, Muzli, Siiimple, UpLabs, Instagram.

### Reading & UX Thinking
When stepping back to think about decisions:
The UX Collective, UX Planet, NN Group, Smashing Magazine, Toptal Blog, Medium, Prototyper.io, Bootcamp (uxdesign.cc), Austin Kleon's books.

## Stack
- Framework: Next.js 14 (App Router) / React 18
- Styling: Tailwind CSS + shadcn/ui
- Animation: GSAP 3.x + ScrollTrigger
- State: Zustand / React Context
- Deploy: Vercel
- Repo: github.com/<org>/<repo>

## Design System
**Never use raw hex values. Always use Tailwind utility classes or CSS variables.**

Colour scale — zinc only unless the project requires a custom palette:
- Background: 
- Surface: 
- Border:  (subtle) /  (strong)
- Text:  →  →  →  → 

Typography:
- Display:  (Inter via next/font)
- Mono:  (JetBrains Mono or system)
- Weight:  to  only — never below 300 or above 500

Spacing: Tailwind scale only (p-1 = 4px base). No arbitrary values.
Radius: rounded · rounded-lg · rounded-xl · rounded-2xl
Elevation: background steps only — no box-shadow


## Component Library
- Use shadcn/ui for all base components
- shadcn MCP connected — query for component API before building
- Custom components live in `/components/ui/custom/`
- Never duplicate a shadcn component — extend it instead

## File Structure
```
app/
  (routes)/
  layout.tsx
  globals.css
components/
  ui/          # shadcn components
  custom/      # project-specific
  sections/    # page sections
lib/
  utils.ts
  gsap.ts      # GSAP context helpers
public/
CLAUDE.md
```

## Deployment Rules
- NEVER delete working code. Wrap in feature flags or rename with `_legacy` suffix.
- Prefer additive changes: new files, new exports, new routes.
- When modifying components: extend props, don't rewrite signatures.
- Each PR must include: what changed, what was preserved, rollback instructions.
- Branch strategy: `feature/*` → preview deploy → PR to `main` → production.
- Vercel previews auto-deploy on every push to any branch.
- Main branch is protected: requires 1 approval + passing Vercel check.

## Animation (GSAP)
- Use GSAP 3.x (`gsap`, `@gsap/react`)
- Register plugins at module level: `gsap.registerPlugin(ScrollTrigger)`
- Wrap React animations in `gsap.context(() => { ... }, containerRef)` and clean up in useEffect return
- Timelines for sequences: `const tl = gsap.timeline({ defaults: { ease: 'power2.out' } })`
- ScrollTrigger pattern:
  ```js
  ScrollTrigger.create({
    trigger: el,
    start: 'top 80%',
    onEnter: () => tl.play()
  })
  ```
- Easing: `power2.out` (enters), `power2.in` (exits), `elastic.out(1, 0.3)` (playful)
- Durations: 0.3s (micro), 0.6s (standard), 1.2s (hero/page)
- Transforms only — never animate `width`, `height`, `top`, `left`
- Clean up: `ctx.revert()` in useEffect cleanup

## Agent Roles
- **Builder Agent**: Implements components, writes GSAP, commits per screen
- **Precision Agent**: Cross-checks design, spacing, tokens, PRD criteria  
- **Reviewer Agent**: Code quality, diff safety, GSAP cleanup patterns

## Memory
- Summaries saved to `/.agent-memory/`
- Summary format: DONE / IN_PROGRESS / BLOCKED / NEXT (≤500 lines)
- Always load latest summary at start of new session
```

---

## Notion PRD Query Patterns {#notion-prd}

### Find PRD by title
```
Tool: notion-search
Query: "PRD <feature name>" OR "<feature name> spec"
Filter: page type, last edited within 30 days
```

### Extract structured content
```
Tool: notion-fetch
URL: <page URL from search result>
```

### Parse Notion blocks → structured brief
Look for these block types in order:
1. `heading_1` / `heading_2` → section markers
2. `bulleted_list_item` under "Goals" → extract goals
3. `numbered_list_item` under "Acceptance Criteria" → extract AC
4. `callout` blocks → key constraints or warnings
5. `image` blocks → design reference URLs

---

## Linear PRD Query Patterns {#linear-prd}

### Find issues
```
Query: project issues where state = "Spec" OR label = "PRD"
Sort by: priority desc
```

### Extract acceptance criteria
Linear stores these in:
- Issue description (markdown)
- Sub-issues (child tasks)
- Linked documents in "Attachments"

### Map to execution plan
```
Linear priority → agent task order
Blocked-by → dependency graph
Estimate → time budget per phase
```

---

## Vercel Configuration {#vercel-config}

### vercel.json for branch previews
```json
{
  "github": {
    "enabled": true,
    "autoAlias": true,
    "silent": false
  },
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "framework": "nextjs"
}
```

### Enable password protection on previews
```json
{
  "protectionBypassForAutomation": true
}
```
Then in Vercel dashboard: Settings → Deployment Protection → Enable for Preview branches.

### GitHub branch protection via CLI
```bash
gh api \
  --method PUT \
  -H "Accept: application/vnd.github+json" \
  /repos/{owner}/{repo}/branches/main/protection \
  -f required_status_checks='{"strict":true,"contexts":["vercel"]}' \
  -f enforce_admins=true \
  -f required_pull_request_reviews='{"dismissal_restrictions":{},"dismiss_stale_reviews":false,"require_code_owner_reviews":false,"required_approving_review_count":1}' \
  -f restrictions=null
```

### Post-deploy checklist
- [ ] Preview URL loads without error
- [ ] All PRD acceptance criteria pass visually
- [ ] GSAP animations play correctly in preview
- [ ] No console errors
- [ ] Mobile viewport looks correct
- [ ] PR opened with preview URL in description
- [ ] Linear/Notion PRD updated with preview link

---

## Memory Summary Format {#memory-format}

Save to `/.agent-memory/session-<ISO-timestamp>.md`:

```markdown
# Agent Memory — <timestamp>

## DONE
- <component/task> — <one line result>
- ...

## IN_PROGRESS
- <component/task> — <current state>

## BLOCKED
- <item> — blocked by: <reason>

## NEXT
1. <task>
2. <task>
3. <task>

## Key Decisions
- <decision and rationale>

## PRD Acceptance Criteria Status
- [x] <criteria 1>
- [ ] <criteria 2>

---
Token budget used: ~<n>k / session
Files changed: <list>
Branch: feature/<n>
```

Max 500 lines. Prune DONE items after 10 entries — keep only last 10.

---

## Figma File Setup {#figma-setup}

### Cover Matching
The cover must be indistinguishable from neighboring files. Use `Figma:get_metadata` or `Figma:get_screenshot` on 2–3 adjacent files to extract:
- Frame dimensions (record exactly)
- Background fill (hex or gradient stops)
- Title font family, weight, size, position
- Subtitle/date position and style
- Any logo, icon, or decorative element placement

Recreate this as a new frame — do not freestyle the cover. Consistency is the goal. If neighboring covers reference a shared variable (e.g. `cover/bg`), use the same variable. If hardcoded, match the hex exactly.

### Page Order (strict)
```
1. 📋 Cover
2. 🗺  Flowchart
3. 🎨 Design System
4. 📐 Wireframes
5. ✦  Designs
6. ⚙️  Specs
7. 🗃  Archive
```
Never reorder. Add sub-pages within a section if needed (e.g. `✦ Designs / Mobile`).

### FigJam Flowchart Guidelines
- Use `Figma:generate_diagram` with `flowchart LR` Mermaid syntax
- Node types: rectangles for screens, diamonds for decisions, rounded for entry/exit
- Max 15 nodes — if more, use swimlanes by user role or flow phase
- Color: neutral for screens, accent for CTAs, red for error states
- Always label edges with the user action that triggers the transition
- Title: `[Feature Name] — User Flow v1`

### Archive Policy
Never delete frames. When deprecating: move to `🗃 Archive`, add a comment with date + reason. Mirrors the no-deletion deployment rule.

---

## GSAP + React Setup Snippet {#gsap-react}

```tsx
// lib/gsap.ts
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

if (typeof window !== 'undefined') {
  gsap.registerPlugin(ScrollTrigger)
}

export { gsap, ScrollTrigger }

// components/AnimatedSection.tsx
import { useEffect, useRef } from 'react'
import { gsap, ScrollTrigger } from '@/lib/gsap'

export function AnimatedSection({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const ctx = gsap.context(() => {
      gsap.from(ref.current, {
        opacity: 0,
        y: 40,
        duration: 0.6,
        ease: 'power2.out',
        scrollTrigger: {
          trigger: ref.current,
          start: 'top 80%',
        }
      })
    }, ref)

    return () => ctx.revert()
  }, [])

  return <div ref={ref}>{children}</div>
}
```


---

## Design System Tokens {#design-tokens}

These tokens are the canonical visual baseline for all output produced by this skill. Reference this section as context when building any UI, document, or component.

**Hard rule: never use raw hex values in components or stylesheets. Always use Tailwind CSS utility classes or CSS variables. Raw hex in component code is a design system violation — same as an off-token spacing value or a bespoke colour.**

### Code blocks
All code and command surfaces must use the shared `.cmd-block` / `pre > code` pattern. Never create bespoke one-off styled containers (e.g. custom install pills, inline code badges with unique backgrounds) — this is a design system violation. Every code surface looks identical: `bg-zinc-950 border border-zinc-800 rounded-lg font-mono text-zinc-200`.

### Colours

Use Tailwind's zinc scale throughout. Never write raw hex — always use the class or variable.

```
Role              Tailwind class        CSS variable            Use
──────────────────────────────────────────────────────────────────────────
Background        bg-black              --color-black           Page surface
Surface           bg-zinc-950           --color-zinc-950        Cards, code blocks
Border subtle     border-zinc-900       --color-zinc-900        Dividers, outlines
Border strong     border-zinc-800       --color-zinc-800        Table rules, blockquotes

Text white        text-white            --color-white           Headings, active
Text primary      text-zinc-200         --color-zinc-200        Strong text, labels
Text secondary    text-zinc-400         --color-zinc-400        Body strong, h3
Text muted        text-zinc-500         --color-zinc-500        Body copy, code text
Text subtle       text-zinc-600         --color-zinc-600        Descriptions, em
Text faint        text-zinc-700         --color-zinc-700        Eyebrows, placeholders
Text ghost        text-zinc-800         --color-zinc-800        Disabled, decorative
```

In tailwind.config.ts extend with semantic aliases so intent is clear in markup:

```ts
theme: {
  extend: {
    colors: {
      surface: 'var(--color-zinc-950)',
      'border-subtle': 'var(--color-zinc-900)',
      'border-strong': 'var(--color-zinc-800)',
      'text-primary': 'var(--color-zinc-200)',
      'text-muted': 'var(--color-zinc-500)',
      'text-faint': 'var(--color-zinc-700)',
    }
  }
}
```

### Typography

```
Font sans    font-sans   (Inter via next/font or system stack)
Font mono    font-mono   (JetBrains Mono or system mono)

H1    text-6xl  / font-light / tracking-tight   / leading-none
H2    text-5xl  / font-light / tracking-tight   / leading-tight
H3    text-3xl  / font-light / tracking-tight   / leading-normal
H4    text-2xl  / font-light / tracking-tight   / leading-normal
H5    text-xl   / font-light / tracking-tight   / leading-relaxed
H6    text-xs   / font-medium / tracking-widest / uppercase        (eyebrow/label)
Body  text-base / font-normal / tracking-normal / leading-normal
Small text-xs   / font-normal / tracking-wide   / leading-tight
```

Never use font-weight below 300 or above 500. Use `font-light`, `font-normal`, `font-medium` only.

### Spacing

Always use Tailwind spacing scale — never arbitrary values unless a token genuinely doesn't exist.

```
xs    p-1  / gap-1   (4px)
sm    p-2  / gap-2   (8px)
md    p-4  / gap-4   (16px)
lg    p-6  / gap-6   (24px)
xl    p-8  / gap-8   (32px)
2xl   p-12 / gap-12  (48px)
3xl   p-16 / gap-16  (64px)
4xl   p-20 / gap-20  (80px)
```

### Border radius

```
sm    rounded     (4px)   inline code, chips
md    rounded-lg  (8px)   inputs, buttons
lg    rounded-xl  (12px)  panels, cards
xl    rounded-2xl (20px)  large cards, modals
```

### Shadows / Elevation

No decorative box shadows. Elevation through background steps only:
`bg-black` → `bg-zinc-950` → `bg-zinc-900` → `bg-zinc-800`

### Motion (GSAP)

Per-element ScrollTrigger pattern — required, no exceptions. See GSAP spec section.

```
Ease in       power2.in
Ease out      power2.out
Ease in-out   power2.inOut
Playful       elastic.out(1, 0.3)

Duration micro    0.3s
Duration standard 0.6s
Duration hero     1.2s

Y offset      4vh on enter (via scrub)
scrub         1.5
```
