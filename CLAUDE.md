# Project Rules

## Process Logging

After completing any task, append a log entry to docs/process_log.md with:
- Timestamp
- The full prompt that initiated the task
- What was built
- Key decisions made and why
- All file paths created or modified

Log entries go newest first.

## Tutorials

After each tool is built, generate a step-by-step tutorial in tutorials/ documenting:
- The exact prompts used
- Key decisions and rationale
- The final output or file path
- Written as if teaching someone to replicate from scratch
- Named sequentially: tutorials/01_short_name.md, 02_short_name.md, etc.

## Guided Tour Page

After all tools are complete, generate an index.html guided tour page that showcases each tool in sequence. For each tool show: the tool name, the problem it solves, the prompt that built it, and an embedded preview of the output.

## Git Discipline

After completing each tool, stage all changes and commit with a descriptive message. Do not wait for multiple tools to accumulate before committing.

## Logo and Layout Standards

These standards apply across all demo repos for consistent Agency State branding.

### Logo Assets

- Dark wordmark: `assets/agency-state-wordmark.svg`
- White wordmark: `assets/agency-state-wordmark-white.svg`
- Monogram/mark: `assets/mark.svg`

### Header

- Agency State wordmark in top left, linked to `https://agencystate.ai`
- Use the dark wordmark on light backgrounds, white wordmark on dark backgrounds
- Position: `absolute` (scrolls with hero), not `fixed`

### Footer

- Left: monogram + clickable wordmark SVG (linked to `https://agencystate.ai`), vertically centered
- Right: "Built with Claude Code" in Inter, `font-size: 0.8125rem`, color `#5C5C5C`
- No URL text — the wordmark link is sufficient
- Footer font is always Inter, regardless of the demo's other fonts

## Fonts

All demo pages and the main website use Inter as the primary font. Never use system fonts (`-apple-system`, `BlinkMacSystemFont`, etc.) as the primary font. Load Inter from Google Fonts.

- Body text: Inter 400
- Bold text: Inter 700
- Headings: Inter 600 or 700