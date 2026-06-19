# CLAUDE.md — AUTOMAT

## Project Overview

AUTOMAT is an automated monitoring system that tracks free AI model availability on [OpenRouter](https://openrouter.ai). It runs weekly scans (every Monday) to detect new and disappeared models, maintains a curated database, and generates French-language analysis reports.

There is **no application code** in this repository — it is a data-only project consisting of JSON data files and Markdown reports, updated by automated Claude Code sessions.

## Repository Structure

```
AUTOMAT/
├── CLAUDE.md                        # This file
└── opencode/
    ├── opencode-config.json         # Default model recommendation config
    ├── opencode-models.json         # Full database of free models
    └── rapport-YYYY-MM-DD.md        # Weekly analysis reports
```

## Key Files

### `opencode/opencode-config.json`
- Specifies the currently recommended free model for coding tasks
- References the `opencode.ai` config schema
- Updated weekly alongside the models database

### `opencode/opencode-models.json`
- Complete list of all free models on OpenRouter
- Each model entry has: `id`, `name`, `context_length`
- Sorted by context length (descending)
- Includes a `count` field and `updated_at` timestamp

### `opencode/rapport-YYYY-MM-DD.md`
- Weekly Markdown reports in French
- Consistent structure: recommended model status, new models, disappeared models, top 5 table, total count, sources, technical notes

## Weekly Update Workflow

Each Monday, a scan is performed:

1. **Collect** current free models from OpenRouter (via public web sources — the API is not accessible from the sandbox environment)
2. **Compare** against the previous week's `opencode-models.json` to detect additions and removals
3. **Evaluate** whether the default recommended model should change, using these priority criteria:
   - Preferred providers: NVIDIA, Google, Meta, OpenAI, Anthropic
   - Large context window
   - High parameter count (dense models preferred over MoE)
   - Strong coding capability
4. **Update** `opencode-config.json` and `opencode-models.json`
5. **Generate** a new `rapport-YYYY-MM-DD.md` report
6. **Commit** with message format: `chore: update free models YYYY-MM-DD`

## Conventions

### Language
- All reports and notes are written in **French**
- File names and JSON keys use **English**

### File Naming
- JSON files: kebab-case (`opencode-config.json`)
- Reports: `rapport-YYYY-MM-DD.md` (ISO date format)

### Commit Messages
- Format: `chore: update free models YYYY-MM-DD`
- Author: `opencode-routine <osvirgil370@gmail.com>`
- All commits reference the Claude Code session that produced them

### Model ID Format
- Uses OpenRouter's model ID format: `provider/model-name:free`
- Config file prefixes with `openrouter/` namespace

### Report Structure
Every report follows this template:
1. Title with date
2. Default model recommendation (previous, new, reasoning)
3. New models with descriptions
4. Disappeared models
5. Top 5 recommended models table (provider, context, parameters)
6. Total free model count
7. Sources (with links)
8. Technical notes about data collection limitations

## Data Collection Notes

- The OpenRouter API (`https://openrouter.ai/api/v1/models`) is **not accessible** from the sandbox execution environment due to network restrictions
- Data is compiled from publicly available web sources
- This limitation is documented in each report's technical notes section

## Important Rules for AI Assistants

1. **Preserve the French language** in all reports and notes within data files
2. **Maintain the existing report structure** — do not reorganize sections
3. **Sort models by context_length** (descending) in `opencode-models.json`
4. **Update the `count` field** in `opencode-models.json` when adding/removing models
5. **Update `updated_at`** timestamps in both JSON files when making changes
6. **Use the established commit message format** for weekly updates
7. **Do not fabricate model data** — only include models verified from sources
8. **Keep the config schema reference** in `opencode-config.json`
