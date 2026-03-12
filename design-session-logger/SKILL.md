---
name: design-session-logger
description: Log design work sessions to your note-taking system. Use when user says "log" or "exit" to save session progress, document design work, or create session summaries. Works with Obsidian, Notion, Confluence, Apple Notes, or any markdown-based system. Trigger on session logging, save progress, design documentation, exit commands, or when user wants to document their work.
---

# Design Session Logger

A flexible skill for logging design sessions to any note-taking system (Obsidian, Notion, Confluence, Apple Notes, etc.).

## First-time Setup

Check for config file at `~/.design-session-logger.json`.

**If config exists**: Read settings and use them.

**If config doesn't exist**: Ask the user:

1. **"Where should I save design logs?"**
   - Get the full path (e.g., `/Users/you/Documents/Obsidian Vault/Design Sessions`)
   - For Notion/Confluence users, suggest a local folder they can import from

2. **"What's your preferred structure?"** Offer these presets:
   - **Project folders** (recommended): `{project}/{page} - {date}.md`
   - **Flat structure**: `{project} - {page} - {date}.md`
   - **Date-based**: `{date}/{project} - {page}.md`
   - **Custom**: Let them define their own pattern

3. **Create the config file**:
```json
{
  "save_location": "/path/to/notes",
  "folder_pattern": "{project}",
  "file_pattern": "{page} - {date}.md",
  "title_format": "# {project} - {page}",
  "date_format": "MMMM DD, YYYY",
  "sections": [
    "What we built",
    "Design decisions",
    "Components changed",
    "Potential next steps",
    "Notes"
  ]
}
```

Save this to `~/.design-session-logger.json` and confirm with the user.

## When User Says "log" or "exit"

### Step 1: Load Configuration
Read `~/.design-session-logger.json` to get settings.

### Step 2: Gather Context
From the current conversation, extract:
- **Project name**: The Figma project or design system being worked on
- **Page name**: The specific page/canvas within that project
- **Current date**: Format according to `date_format` from config
- **Session content**:
  - What was built (components, layouts, features)
  - Design decisions made (approach, rationale, tradeoffs)
  - Components changed (what was created, modified, or removed)
  - Potential next steps (what's planned or suggested)
  - Notes (any important context, blockers, or observations)

### Step 3: Build File Path
Use the config patterns to construct the path:
- Replace `{project}` with project name
- Replace `{page}` with page name
- Replace `{date}` with formatted date
- Combine: `{save_location}/{folder_pattern}/{file_pattern}`

Example: `/Users/you/Documents/Obsidian Vault/Design Sessions/Agentic workflow/MCP test - March 12, 2026.md`

### Step 4: Check for Existing File
If working on the same project-page-date:
- **UPDATE** the existing file (merge new content with existing)
- Preserve existing content, add new work

If new day or new project-page:
- **CREATE** a new file

### Step 5: Format Content
Use the title format and sections from config. Example:

```markdown
# {project} - {page}

## What we built
- [Bullet points of components, layouts, features created]

## Design decisions
- [Key choices made, rationale, approach]

## Components changed
- [What was created, modified, updated]

## Potential next steps
- [ ] [Checkbox list of future work]

## Notes
- [Context, blockers, observations]
```

**Important formatting rules:**
- Use sentence case for all section headings (from config)
- Keep content simple and flat with bullet points
- No complex formatting or nested structures
- Focus on high-level decisions and changes, not minutiae

### Step 6: Write the File
Create any necessary folders, then write the file.

### Step 7: Confirm and Continue
- If user said **"log"**: Confirm the save and continue the session
- If user said **"exit"**: Confirm the save and say goodbye

## Platform-Specific Notes

### Obsidian
Files are saved directly as `.md` and will appear immediately in Obsidian.

### Notion
- Save as `.md` files to the configured location
- User can drag-and-drop into Notion or use Notion's import feature
- Alternatively, if user provides a Notion API token, you could integrate directly

### Confluence
- Generate markdown files
- User can copy-paste content into Confluence pages
- Preserve markdown formatting for easy conversion

### Apple Notes
- Save as `.txt` or `.md` files
- User can copy-paste into Notes
- Alternatively, use AppleScript to create notes directly (if user wants automation)

### Other Systems
- Save as plain text or markdown
- Provide clear file location so user can import/copy as needed

## Updating Configuration

User can edit `~/.design-session-logger.json` anytime to change:
- Save location
- Folder/file structure
- Section names
- Date format
- Title format

Or ask you to update it for them by saying "update my design log config".

## Examples

**Example 1: Obsidian with project folders**
```
Config:
{
  "save_location": "/Users/you/Documents/Obsidian Vault/Design Sessions",
  "folder_pattern": "{project}",
  "file_pattern": "{page} - {date}.md"
}

Result: Design Sessions/Agentic workflow/Dashboard - March 12, 2026.md
```

**Example 2: Flat structure for Notion import**
```
Config:
{
  "save_location": "/Users/you/Desktop/Design Logs",
  "folder_pattern": "",
  "file_pattern": "{project} - {page} - {date}.md"
}

Result: Design Logs/Agentic workflow - Dashboard - March 12, 2026.md
```

**Example 3: Date-based organization**
```
Config:
{
  "save_location": "/Users/you/Documents/Notes",
  "folder_pattern": "{date}",
  "file_pattern": "{project} - {page}.md"
}

Result: Notes/March 12, 2026/Agentic workflow - Dashboard.md
```

## Troubleshooting

**Config file missing or corrupted**: Walk through setup again.

**Path doesn't exist**: Create the directories as needed.

**Can't determine project/page**: Ask user to clarify what project and page they're working on.

**First time in session**: If no design work has happened yet, ask user if they want to log configuration changes or skip logging this session.
