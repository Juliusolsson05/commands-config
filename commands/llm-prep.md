---
description: Crawl a codebase and produce a markdown context document for pasting into another LLM
---

Generate a context document from this project for pasting into an LLM web UI.

**IMPORTANT: Do NOT use the Write tool or Read tool to build this file. Use a single Bash command to generate the entire document.**

## How to generate

Run a single bash script that:
1. Writes the tree structure at the top
2. Finds all source files
3. Concatenates their contents with headers

Use this exact pattern (adjust the file extensions for the project type):

```bash
IGNORE="node_modules|.git|dist|build|__pycache__|venv|.venv|.env|*.pyc|.DS_Store|.idea|.vscode|coverage|.next|.nuxt|.cache|out|target|vendor|*.log|logs|*.egg-info|.pytest_cache|.mypy_cache|htmlcov|.nyc_output|bower_components|.terraform|.serverless|context.md"

{
  echo "# Project Context: $(basename $(pwd))"
  echo "Generated: $(date)"
  echo ""
  echo "## Project Structure"
  echo '```'
  tree -L 3 -I "$IGNORE" --charset ascii 2>/dev/null || find . -not -path '*/node_modules/*' -not -path '*/.git/*' -not -path '*/dist/*' -not -path '*/__pycache__/*' -not -path '*/venv/*' -type f | head -200
  echo '```'
  echo ""
  echo "## File Contents"
  echo ""
  find . -type f \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" -o -name "*.java" -o -name "*.c" -o -name "*.cpp" -o -name "*.h" -o -name "*.rb" -o -name "*.php" -o -name "*.swift" -o -name "*.kt" -o -name "*.sh" -o -name "*.sql" -o -name "*.graphql" -o -name "*.proto" -o -name "*.html" -o -name "*.css" -o -name "*.scss" -o -name "*.json" -o -name "*.yaml" -o -name "*.yml" -o -name "*.toml" -o -name "*.md" -o -name "*.txt" -o -name "*.xml" -o -name "*.env.example" -o -name "Dockerfile" -o -name "Makefile" \) \
    -not -path '*/node_modules/*' \
    -not -path '*/.git/*' \
    -not -path '*/dist/*' \
    -not -path '*/build/*' \
    -not -path '*/__pycache__/*' \
    -not -path '*/venv/*' \
    -not -path '*/.venv/*' \
    -not -path '*/coverage/*' \
    -not -path '*/.next/*' \
    -not -path '*/.cache/*' \
    -not -path '*/target/*' \
    -not -path '*/vendor/*' \
    -not -path '*/*.egg-info/*' \
    -not -path '*/context.md' \
    -not -name 'package-lock.json' \
    -not -name 'yarn.lock' \
    -not -name 'pnpm-lock.yaml' \
    -not -name 'poetry.lock' \
    -not -name 'Cargo.lock' \
    -not -name 'go.sum' \
    -not -name 'composer.lock' \
    | sort | while read -r file; do
      echo "### $file"
      echo '```'
      cat "$file"
      echo ""
      echo '```'
      echo ""
    done
} > context.md

wc -c context.md | awk '{printf "Generated context.md — %d characters (%d KB)\n", $1, $1/1024}'
wc -l context.md | awk '{printf "Lines: %d\n", $1}'
```

Adapt the file extensions in the `find` command based on the project. For example, a Python-only project only needs `*.py *.txt *.md *.toml *.yaml *.yml *.json`.

## If I specify files or directories

$ARGUMENTS

If arguments are provided, replace the `find` command with those specific paths. Still include the tree at the top.

## After generating

Tell me the file count, total size, and line count.
