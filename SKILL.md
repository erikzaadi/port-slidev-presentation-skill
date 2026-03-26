---
name: slidev-presentation
description: Create, run, and export Markdown-based presentations using Slidev. Use when building, previewing, or running Slidev presentations. Triggers include "create a presentation", "run slidev", "preview slides", "slidev command", "export to PDF", "Slidev deck", "turn insights into slides", "presentation from notes", "make a presentation from this", or "create slides from [content]".
---

# Slidev presentation

Create Markdown-based presentations using Slidev with live preview, syntax highlighting, and export capabilities.

## Workflow order

**Don't ask before starting. Build first, refine after.**

1. Extract topic, audience, and key points from the user's prompt — make reasonable assumptions
2. Create `slides.md` immediately (8–12 slides, Port theme, pick images from the list in step 1)
3. Run a one-shot compile check, then give the user the command to start the dev server themselves
4. **Only after giving the user the command**, ask clarifying questions:
   - Who is the audience?
   - Are there specific points or data you want emphasized?
   - Any sections to add, remove, or reorder?

Questions are more productive when the user is looking at a live draft — feedback becomes concrete instead of abstract.

**Default assumptions when not specified:**
- Brand style: Port branded
- Slide count: 8–12
- Code examples: only if topic warrants it
- Tone: professional, direct, no marketing speak

## Bootstrap (first time only)

Before proceeding, check whether the skill is already installed:

```bash
ls ~/.claude/skills/slidev-presentation
```

If the directory does not exist, clone it:
```bash
git clone https://github.com/port-labs/port-slidev-presentation-skill.git \
  ~/.claude/skills/slidev-presentation
```

**In either case**, re-read the skill from the local copy before continuing:

```
~/.claude/skills/slidev-presentation/SKILL.md
```

The skill lives at `~/.claude/skills/slidev-presentation` — never look for it relative to the project directory.

## Process

### 1. Set up the presentation with Port theme

Create the presentation folder and symlink the themes and public directories from the skill:

```bash
mkdir -p [name]

# Symlink the entire themes dir and the pre-built public dir (images already symlinked inside)
ln -s ~/.claude/skills/slidev-presentation/themes [name]/themes
ln -s ~/.claude/skills/slidev-presentation/templates/public [name]/public
```

Create `slides.md` with this frontmatter:

```yaml
---
theme: ./themes/port
title: Presentation Title
---
```

This mirrors the convention used in the skill's own template (`port-template.md`). The `public/` symlink gives access to all theme images without any glob expansion.

The Port theme provides:
- Pre-styled layouts (cover, section, default)
- Reusable components (Grid, FeatureCard, MetricCard, Tag, StepItem, ImpactBox, etc.)
- Consistent brand colors and typography
- 80+ Port product screenshots in `themes/port/public/images/` — symlink them as above

**See:** [themes/port/README.md](themes/port/README.md) for full component documentation.

**Available images:** All images are in `public/images/` (symlinked from the skill's templates). Reference them in slides as `<Image src="/images/[name]" />`. Key images:
- `hero-3d-isometric-platform-blocks.jpg` — 3D Port hero, use on cover slides
- `cicd-pipeline-self-healing-status.png` — CI/CD pipeline with AI self-healing
- `tasks-assignment-human-vs-ai-donut.png` — Human vs AI task split donut chart
- `ai-suggested-playbooks-sre-agent.png` — SRE agent playbook suggestions
- `agents-context-lake-diagram.png` — AI providers connected to context lake
- See `templates/port-template.md` image catalogue slides for the full list

### 2. Use template components (not custom CSS)

**CRITICAL: Never use inline Tailwind classes in slides.md.** All styling must go through theme components. This ensures consistency and makes slides easy to maintain.

| Instead of... | Use... |
|---------------|--------|
| `<div class="grid grid-cols-3 gap-4">` | `<Grid cols="3" gap="4">` |
| `<p class="text-lg text-gray-600">` | `<Paragraph>` |
| `<div class="mt-8">` | `<Space size="large" />` |
| `<p class="text-xl font-bold text-center">` | `<Highlight>` |
| `<img class="max-h-96 rounded-lg">` | `<Image size="large" />` |
| `<div class="bg-blue-100 rounded-xl p-5">` | `<FeatureCard color="blue">` |
| `<div class="bg-black text-white p-6">` | `<ImpactBox>` |
| `<span class="bg-blue-100 px-3 py-1 rounded-full">` | `<Tag color="blue">` |
| Video/content placeholder div | `<Placeholder title="..." />` |

**Available components:**

**Layout:**
- `Grid` - Responsive grid layout (cols, gap props)
- `Stack` - Vertical stacking with consistent gaps (gap: small/medium/large)
- `Space` - Vertical spacing between elements (size: small/medium/large)

**Text:**
- `Subtitle` - Muted text below headings
- `Paragraph` - Body text with proper margins (center, bold props)
- `Highlight` - Centered emphasized text for key questions (weight: light/normal/bold)
- `Note` - Italic muted text for caveats

**Cards:**
- `FeatureCard` - Colored card with icon (color, variant: default/pillar, size: default/compact)
- `MetricCard` - Big number with label
- `StepItem` - Numbered step with title
- `AgendaItem` - Colored agenda boxes

**Media:**
- `Image` - Images with consistent styling (src, size: small/medium/large/full, center, rounded)
- `PortLogo` - Port logo (type: svg/png, color: black/white, size)
- `Placeholder` - Content placeholder (title, subtitle)

**Accents:**
- `Tag` - Pill-shaped labels
- `ImpactBox` - Black box for key takeaways (center, spacing: default/small/none)
- `ColorDots` - Decorative cover element

**Example slide using components:**

```markdown
# Slide title

<Subtitle>Supporting context for the title</Subtitle>

<Space size="large" />

<Grid cols="2" gap="4">
  <Image src="/images/example.png" alt="Description" />
  <Stack>
    <FeatureCard icon="🎯" title="Point one" color="blue" size="compact">
      Brief description
    </FeatureCard>
    <FeatureCard icon="💡" title="Point two" color="green" size="compact">
      Another description
    </FeatureCard>
  </Stack>
</Grid>

<ImpactBox center>
  Key takeaway for the audience.
</ImpactBox>

<Note>Optional caveat or footnote.</Note>
```

### 3. Apply layouts

Add `layout` frontmatter per slide:

| Layout | Purpose |
|--------|---------|
| `cover` | Title/intro slide with large heading |
| `default` | Standard content slide |
| `section` | Section divider between topics |
| `two-cols` | Two equal columns |
| `image-right` | Content on left, image on right |
| `image-left` | Content on right, image on left |
| `wide-image` | Full-bleed image with content overlay (Port theme) |
| `center` | Centered content |

Two-column example:

```markdown
---
layout: two-cols
---

# Left Column

Content here

::right::

# Right Column

More content
```

### 4. Add code blocks

Use line highlighting for step-by-step reveals:

```markdown
```python {1-3|5-7}
# Lines 1-3 highlighted first
def example():
    pass

# Then lines 5-7
def another():
    pass
```
```

### 5. Add charts and diagrams with Mermaid

Slidev has built-in support for Mermaid diagrams. Use them for data visualization, flowcharts, and process diagrams.

Always use `%%{init: ...}%%` to set explicit colors — Mermaid bakes these into the SVG at render time, so they work consistently across light and dark mode without any CSS overrides.

**Pie chart example:**

```markdown
# Data breakdown

<Subtitle>Project time allocation</Subtitle>

\`\`\`mermaid
%%{init: {'theme':'base', 'themeVariables': { 'pie1':'#dbeafe', 'pie2':'#fce7f3', 'pie3':'#dcfce7', 'pie4':'#fef3c7', 'pie5':'#f3e8ff', 'primaryTextColor':'#1f2937', 'pieSectionTextColor':'#1f2937', 'pieTitleTextColor':'#1f2937', 'pieLegendTextColor':'#1f2937'}}}%%
pie title Project Distribution
    "Development" : 35
    "Testing" : 20
    "Documentation" : 15
    "Deployment" : 20
    "Maintenance" : 10
\`\`\`
```

**Line chart (improvement over time):**

```markdown
# Improvement over time

<Subtitle>Deployment frequency after platform adoption</Subtitle>

\`\`\`mermaid
%%{init: {'theme':'base', 'xychart-beta': {'height': 250}, 'themeVariables': { 'primaryColor':'#3b82f6', 'primaryTextColor':'#1f2937', 'lineColor':'#3b82f6', 'background':'transparent'}}}%%
xychart-beta
    title "Deployments per week"
    x-axis ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug"]
    y-axis "Deployments" 0 --> 50
    line [4, 6, 5, 9, 14, 22, 35, 48]
\`\`\`

**Note:** Always set `'xychart-beta': {'height': 250}` in the init block. Mermaid renders xychart inside a Shadow DOM so CSS cannot control the size — explicit height in the config is the only reliable way.
```

**Port color palette for themeVariables:**
- Blue: `#3b82f6` / light: `#dbeafe`
- Pink: `#ec4899` / light: `#fce7f3`
- Green: `#22c55e` / light: `#dcfce7`
- Yellow: `#eab308` / light: `#fef3c7`
- Purple: `#a855f7` / light: `#f3e8ff`

**Other Mermaid diagram types:**
- Flowcharts: `graph TD` or `graph LR`
- Sequence diagrams: `sequenceDiagram`
- Gantt charts: `gantt`
- Class diagrams: `classDiagram`
- State diagrams: `stateDiagram-v2`

### 6. Add speaker notes

Include notes below each slide using HTML comment:

```markdown
# Slide Title

Visible content

<!--
Speaker notes go here.
Only visible in presenter mode.
-->
```

### 7. Preview and export

After creating `slides.md`:

1. **Run a one-shot compile check** — this exits automatically, it is not a dev server. Must be run from inside the presentation folder:

```bash
cd [name] && npx @slidev/cli build --skip-download 2>&1 | head -30
```

If it exits without errors, the slides are valid.

2. **Give the user the command to start the dev server themselves** — do not run it in the background, do not start it as a long-running process:

```bash
cd [name] && npx @slidev/cli slides.md --port [random-port]
```

**Always use a random port** (e.g., 3031-3099) since 3030 is often occupied.

3. **Once you've given the user the command, ask your clarifying questions** (see Workflow order above)

The dev server provides:
- **Slides**: http://localhost:[port]/
- **Presenter mode**: http://localhost:[port]/presenter/
- **Overview**: http://localhost:[port]/overview/

**Export commands:**

```bash
# Export to PDF
npx @slidev/cli export slides.md --output presentation.pdf

# Export to PNG (one per slide)
npx @slidev/cli export slides.md --format png

# Export with click animations expanded
npx @slidev/cli export slides.md --with-clicks --output detailed.pdf
```

See [references/export-guide.md](references/export-guide.md) for complete export options.

## Output format

Each presentation folder contains:

```
[name]/
├── slides.md          # Main presentation file
├── [name].pdf         # Exported PDF
├── themes -> ~/.claude/skills/slidev-presentation/themes  # All themes
├── public -> ~/.claude/skills/slidev-presentation/templates/public  # Images
└── components/        # Custom Vue components (optional)
```

The presentation folder is created directly in the project root (or wherever the user specifies) — not nested under `outputs/` or any other subdirectory unless explicitly requested.

`public/` is symlinked from the skill's templates folder, which already contains all theme images. Only add presentation-specific images by replacing the symlink with a real folder and copying content across.

## Multi-slide editing

When making changes across multiple slides, use the Task tool to spawn sub-agents. This preserves your main context and allows parallel edits.

**Pattern:**
```
Task(subagent_type="general-purpose", prompt="Edit slide X in [file] to [specific change]")
```

**When to use sub-agents:**
- 3+ slides need changes
- Each slide has independent fixes
- User provides a list of feedback items

**Instructions for sub-agents:**
- Give specific, text-only instructions (no code blocks needed)
- Describe the exact change: what element, what property, what value
- Reference line numbers or slide numbers for clarity

## Port brand guidelines

These rules come from the official Port deck template:

**Design:**
- Use 1 clear image per slide
- Font: DM Sans only — never switch to other fonts
- Avoid complex charts — keep data simple and readable
- Avoid using bold style and exclamation marks
- Use capital letters only at the beginning of a sentence or a name

**Writing:**
- Keep texts short and crisp
- No marketing speak or inflated language
- Text is specific, not generic
- Descriptions are down-to-earth

**Presentation:**
- Transition: use `fade-out` (the Slidev equivalent of "Dissolved") — already set in the template frontmatter
- Present in slideshow/fullscreen mode
- Stay in 1-3 consistent layouts per deck — adapt content to the layout, not the other way around

## Quality checklist

**Structure:**
- [ ] One main idea per slide
- [ ] Port brand colors applied
- [ ] Consistent layout usage throughout (1-3 layouts max)
- [ ] No walls of text (max 5-7 bullet points)

**Brand:**
- [ ] DM Sans font (set in frontmatter — do not override)
- [ ] No bold text or exclamation marks
- [ ] Capital letters only at sentence start or proper names
- [ ] 1 image per slide maximum

**Layout:**
- [ ] No panel overflow (see [troubleshooting guide](references/layout-troubleshooting.md))
- [ ] Cards align horizontally (use flex-col + min-height + mt-auto)
- [ ] Compact sizing for 4+ items (py-2, gap-2 instead of py-8, gap-8)

**Content:**
- [ ] Text is specific, not generic
- [ ] No marketing speak or inflated language
- [ ] Follow writing guidelines for all text

## Extending the theme

**Rule: If you can't build it with existing components, create a new component.**

When you need a pattern that doesn't exist:

1. **First check if existing components can work** - Most layouts use Grid + Stack + existing components
2. **If not, create a new component** in `themes/port/components/`
   - Add props for variations (size, color, spacing)
   - Include scoped styles in the component
   - Test it works in isolation
3. **Document the component** in `themes/port/README.md`

**Never:**
- Add inline Tailwind classes (`class="..."`) to slides.md
- Add inline `<style>` blocks to individual presentations
- Use raw HTML tags (`<div>`, `<p>`, `<img>`) with custom styling

**Why:** Theme components ensure consistency across all presentations, handle responsive behavior, and make maintenance easier. One style change in a component updates all slides using it.

## Insights-to-slides workflow

When starting from raw content (meeting notes, research, call transcripts, analysis documents) rather than a predefined outline, follow the guided insights-to-slides workflow. This adds a planning phase before slide creation: analyze the source material, propose a narrative arc, gather audience/goal requirements, identify visual opportunities, and then build slides using the standard process above.

**See:** [references/insights-to-slides.md](references/insights-to-slides.md) for the full workflow, plan template, and content-specific quality checklist.

## References

- [Port theme README](themes/port/README.md) - Full component documentation with examples
- [Layout troubleshooting](references/layout-troubleshooting.md) - Fixes for overflow, alignment, and common issues
- [Narrative framework](references/narrative-framework.md) - Andy Raskin's five-act structure for compelling presentations
- [Export guide](references/export-guide.md) - PDF, PNG, and PowerPoint export options
- [Insights-to-slides workflow](references/insights-to-slides.md) - Guided workflow for transforming raw content into presentations
