````skill
---
name: tool-usage
description: "Development tool preferences and execution patterns. Use when choosing between VS Code tools and terminal commands, handling file operations, searches, or Hugo commands."
---

# Tool Usage Guidelines

Standard tool-vs-terminal decision framework for this repository.

---

## Tool-First Approach

Use specialized VS Code tools instead of terminal commands for file operations
and code navigation. Tools provide structured output and integrated error
reporting that raw terminal commands do not.

| Task | Use This Tool | Not This |
|------|--------------|----------|
| Read/edit files | `read_file`, `replace_string_in_file`, `create_file` | `cat`, `sed`, `echo` |
| Check errors | `get_errors` tool | — |
| Search code/content | `semantic_search`, `grep_search` | `grep` in terminal |
| Find files | `file_search`, `list_dir` | `ls`, `find` in terminal |
| Git status | `get_changed_files` | `git status`, `git diff` |

---

## When Terminal Is Appropriate

The terminal is the right tool for Hugo operations, package management,
environment setup, and commands with no tool equivalent.

### Hugo Commands

| Command | When to Use |
|---|---|
| `hugo version` | Verify Hugo installation and version |
| `hugo mod init` | Initialize Hugo modules (once, during scaffold) |
| `hugo mod get` | Install/update theme module |
| `hugo server` | Local development server (run as background process) |
| `hugo` | Build site, verify no errors after changes |
| `hugo --minify` | Production build (used by GitHub Actions) |

Run `hugo server` with `isBackground=true` — it's a long-running process.
Run `hugo` (build only) with `isBackground=false` — it completes quickly
and its output confirms success or shows errors.

### Go Module Commands

| Command | When to Use |
|---|---|
| `go version` | Verify Go is installed (required for Hugo modules) |
| `go mod tidy` | Clean up module dependencies |

### DNS Verification

| Command | When to Use |
|---|---|
| `dig jackpines.info +short` | Verify A records point to GitHub Pages IPs |
| `dig www.jackpines.info +short` | Verify CNAME record |
| `curl -sI https://jackpines.info` | Check HTTP response headers |

### Other Terminal Uses

- **Git operations:** `git add`, `git commit`, `git push` — use terminal
  for write operations; use `get_changed_files` for read operations
- **Package installation:** `brew install hugo`, `go install`, etc.
- **Background processes:** `hugo server` with `isBackground=true`
- **File copying from Obsidian vault:** `cp` for bulk content migration
  (use `create_file` for new files authored in-repo)

---

## Script Handling

| Script Size | Approach |
|---|---|
| ≤ 10 lines | Run directly in terminal |
| > 10 lines | Create a script file, then execute it |

**For long scripts:**
1. Store scripts in `.copilot/scripts/` (git-ignored)
2. Use `create_file` to write the script
3. Use `run_in_terminal` to execute it
4. This prevents terminal buffer overflow

---

## Content File Handling

### Copying from Obsidian Vault

The source Markdown files live in an Obsidian vault outside this repo. When
migrating content:

- Use `read_file` to inspect source files (check frontmatter, content)
- Use `create_file` to write the corrected version into `content/posts/`
- Do not `cp` raw Obsidian files — they need frontmatter fixes and link
  rewrites that should be applied during the copy

### Editing Content In-Repo

- Use `read_file` + `replace_string_in_file` for targeted fixes
- Use `grep_search` to find patterns across multiple content files
  (e.g., broken image URLs, Obsidian-style links)

---

## Why This Matters

- **Faster execution**: Tools are optimized for VS Code integration
- **Better context**: Structured data instead of raw text parsing
- **Error handling**: Built-in validation catches issues early
- **Iteration speed**: Especially impactful for file operations
````
