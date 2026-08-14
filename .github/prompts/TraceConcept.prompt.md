---
name: TraceConcept
description: Runs the AI-driven Traceability pipeline for a single concept file - parses it, generates backlog drafts, and reports drift.
agent: agent
---

Run the AI-driven Traceability toolkit for the concept file at `${input:conceptPath:Path to the concept Markdown file}`.

Steps:
1. Import the module at `scripts/traceability/AasTwinEngine.Traceability.psd1`.
2. Parse the concept file with `Convert-MarkdownToConceptModel -Path "${input:conceptPath}"`.
3. Generate template-conformant backlog drafts with `ConvertTo-BacklogDrafts` into `scripts/traceability/out`.
4. If the quality gate fails, report the offending line numbers and stop.
5. Summarize the parsed concept elements (counts by Type) and the generated drafts.

Do not create issues or push anything. Only parse, generate drafts, and report.
