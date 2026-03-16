# claude-code-initialization-notes

This note documents my personal research on `Claude Code` initialization and configuration for software development. All resources were manually reviewed from the official documentation.

---

## ==&nbsp;Memory Locations&nbsp;==

Reference: `https://code.claude.com/docs/en/memory`

#### User-Specific Setting

Located at `~/.claude/CLAUDE.md`.

Used for **personal coding preferences, code style defaults, and tooling shortcuts**.  
These settings apply across all projects for the current user.

#### Project-Specific Setting

Located at `./CLAUDE.md` or `./.claude/CLAUDE.md`.

Used for **project architecture, coding standards, repository-specific rules, and common workflows**.  
These settings apply only to the current project.

#### Top-Level Memory (Enterprise Only)

Located at `/Library/Application Support/ClaudeCode/CLAUDE.md` on macOS, `C:\Program Files\ClaudeCode\CLAUDE.md` on Windows, and `/etc/claude-code/CLAUDE.md` on Linux.

Typically used for **organization-wide coding standards, security policies, and compliance requirements**.

## ==&nbsp;High-Efficiency Instruction Practices&nbsp;==

#### Suggestions

1. Keep each `CLAUDE.md` file under about 200 lines. Use `@path/to/import` for larger content.
2. Use proper `Markdown` structure so the instructions are easier to read and follow.
3. Write specific technical instructions instead of vague natural language.
   - “Use 2-space indentation” instead of “Format code properly”
   - “Run `npm test` before committing” instead of “Test your changes”
   - “API handlers live in `src/api/handlers/`” instead of “Keep files organized”
4. Avoid conflicting rules across `CLAUDE.md` and `.claude/rules/`.