---
description: Crawl a codebase and produce a markdown context document for pasting into another LLM
---

Crawl this project and produce a single markdown document that can be pasted into an LLM web UI (Claude.ai, ChatGPT, etc.) as context.

## What to do

1. **Show the project tree.** Run `tree -L 3 -I "node_modules|.git|dist|build|__pycache__|venv|.venv|env|.env|*.pyc|.DS_Store|.idea|.vscode|coverage|.next|.nuxt|.cache|out|target|vendor|bin|*.log|logs|*.egg-info|.pytest_cache|.mypy_cache|htmlcov|.nyc_output|bower_components|.terraform|.serverless" --charset ascii` or equivalent. If `tree` is not available, use Glob to list the structure.

2. **Collect all text files.** Read every source file in the project (code, config, docs). Skip binary files, images, lock files (`package-lock.json`, `yarn.lock`, `poetry.lock`, `Cargo.lock`, `go.sum`), and anything in the ignored directories above.

3. **Format as a single markdown document** with this structure:

```
# Project Context: [project name]
Generated: [date]

## Project Structure
[tree output]

## File Contents

### path/to/file.ext
\`\`\`[language]
[file contents]
\`\`\`

### path/to/another.ext
\`\`\`[language]
[file contents]
\`\`\`
```

4. **If files are large (>500 lines),** include a summary comment at the top noting what the file does, then include the full content.

5. **If the total output would exceed ~100K characters,** prioritize: README, config files, entry points, then source files by directory depth (shallower first). Note what was skipped.

## If I specify files or directories

$ARGUMENTS

If arguments are provided, focus only on those paths instead of the full project. Still include the project tree for orientation.

## Output

Write the result to a file called `context.md` in the project root, and also print it to the terminal. Tell me the character count and file count so I know if it fits in a single paste.
