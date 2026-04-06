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