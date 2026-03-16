# Product Design Skills

A collection of Claude Code skills for product designers working with Figma, design systems, and design documentation.

## Skills

### Design Session Logger

Automatically log your design work sessions to any note-taking system (Obsidian, Notion, Confluence, Apple Notes, etc.).

**Features:**
- Flexible configuration for any note-taking system
- **Notion integration**: Automatically sync logs to Notion via API
- Dual sync: Save to both Obsidian (local) and Notion (cloud) simultaneously
- Automatic extraction of design work from conversations
- Customizable folder structures and file naming
- Smart updates: merges new work into existing session logs
- Simple bullet-point format focused on decisions and changes

**Usage:**
1. Install the skill
2. Say "log" to save your progress and continue working
3. Say "exit" to save your progress and end the session

**First-time setup** will prompt you for:
- Where to save logs
- What folder/file structure you prefer
- Custom sections (optional)

See [design-session-logger/SKILL.md](./design-session-logger/SKILL.md) for full documentation.

## Installation

### Installing Individual Skills

1. Clone this repository or download the skill folder you want
2. Copy the skill folder to your Claude plugins directory:
   ```bash
   cp -r design-session-logger ~/.claude/plugins/product-design-skills/skills/
   ```
3. Restart Claude Code or reload skills

### Installing All Skills

```bash
git clone https://github.com/yourusername/product-design-skills.git
cd product-design-skills
# Copy all skills to Claude plugins directory
mkdir -p ~/.claude/plugins/product-design-skills/skills
cp -r */SKILL.md ~/.claude/plugins/product-design-skills/skills/
```

## Contributing

Have a design skill idea or improvement? Contributions welcome!

1. Fork the repository
2. Create a new skill folder with `SKILL.md`
3. Follow the [Claude Code skill format](https://github.com/anthropics/claude-code)
4. Submit a pull request

## Skill Format

Each skill should:
- Live in its own folder
- Have a `SKILL.md` with YAML frontmatter
- Include clear documentation and examples
- Be general-purpose (not overly specific to one workflow)

## License

MIT
