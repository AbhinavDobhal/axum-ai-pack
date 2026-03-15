# Axium Cross-IDE AI Pack

This folder is a zip-ready export bundle for reusing the repository AI guidance across editors and agent tools.

Canonical source used:
- .github/copilot-instructions.md

Included mirrors:
- copilot/.github/copilot-instructions.md
- agents/AGENTS.md
- claude/CLAUDE.md
- cursor/.cursorrules
- windsurf/.windsurfrules
- cline/.clinerules/project.md

How to use:
- GitHub Copilot: copy copilot/.github/copilot-instructions.md to .github/copilot-instructions.md
- AGENTS-compatible tools: copy agents/AGENTS.md to AGENTS.md at repo root
- Claude: copy claude/CLAUDE.md to CLAUDE.md at repo root
- Cursor: copy cursor/.cursorrules to .cursorrules at repo root
- Windsurf: copy windsurf/.windsurfrules to .windsurfrules at repo root
- Cline: copy cline/.clinerules/project.md into .clinerules/project.md

Sync policy:
- Update .github/copilot-instructions.md first.
- Regenerate mirrors after canonical changes.

Status:
- This folder is ready to zip.
