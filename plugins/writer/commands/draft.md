Invoke the writer skill and follow it exactly.

Execute the full workflow:
1. Context Ingestion - scan project folder, clone GitHub repo, generate inventory.md
2. Scoping - ask research question, key findings, constraints, generate scope.md
3. Literature Review - process user-provided PDFs via subagents, generate notes and drafts/introduction.md
4. Code Analysis - analyze GitHub repo, generate notes and drafts/methods.md
5. Results Interpretation - analyze data and figures, generate notes and drafts/results.md
6. Synthesis - integrate all notes, generate drafts/discussion.md and drafts/abstract.md
7. Assembly - combine all drafts into manuscript.md

Ask the user for the project folder path if not already in a project directory.
