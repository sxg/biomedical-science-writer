# Science Plugins

A marketplace of Claude Code plugins for scientific research and writing.

## Installation

### Add the Marketplace

```bash
/plugin marketplace add sxg/biomedical-science-writer
```

### Install a Plugin

```bash
/plugin install writer@science
```

## Available Plugins

| Plugin | Description | Version |
|--------|-------------|---------|
| [writer](./plugins/writer/) | Draft biomedical research manuscripts with specialist agents | 2.3.0 |

## Plugin Details

### writer

Draft biomedical research manuscripts from organized project folders containing papers, data, figures, and GitHub code repositories.

**Features:**
- 8-step workflow: Context ingestion, Scoping, Literature review, Code analysis, Results interpretation, Synthesis, Academic review, Assembly
- Specialist agents: Biostatistician for statistical validation, Academic reviewer for publication readiness
- Subagent-based literature review (processes user-provided PDFs via isolated subagents)
- IRB document support with ethics statement auto-population

**Commands:**
- `/writer:draft` - Full workflow
- `/writer:literature` - Literature review only
- `/writer:methods` - Code analysis only
- `/writer:results` - Results interpretation only
- `/writer:synthesize` - Generate Discussion/Abstract
- `/writer:assemble` - Final assembly

[View full documentation](./plugins/writer/README.md)

## Adding New Plugins

To add a new plugin to this marketplace:

1. Create a directory under `plugins/`:
   ```
   plugins/my-new-plugin/
   ├── .claude-plugin/
   │   └── plugin.json
   ├── commands/
   ├── skills/
   └── agents/
   ```

2. Add the plugin entry to `.claude-plugin/marketplace.json`:
   ```json
   {
     "name": "my-new-plugin",
     "source": "my-new-plugin",
     "description": "Plugin description",
     "version": "1.0.0"
   }
   ```

## License

MIT
