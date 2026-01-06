Invoke the biomedical-science-writer:literature-review skill and follow it exactly.

Prerequisites: scope.md must exist. If not, run /biomedical-science-writer:draft first or create scope.md manually.

Process user-provided PDF papers in papers/ folder via isolated subagents:
- Generate notes/papers/*.md for each PDF (one subagent per paper)
- Synthesize findings into notes/literature-synthesis.md
- Draft drafts/introduction.md
