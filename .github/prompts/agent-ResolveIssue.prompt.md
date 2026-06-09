---
name: ResolveIssue
description: Resolves the Issue with the given Issue number from the Backlog in PM repository, pushes the changes, and creates a draft PR.
agent: agent
---

Assign the following issue https://github.com/AAS-TwinEngine/AAS.TwinEngine.PM/issues/${input:issue:Type the issue number from the backlog here} to Copilot and resolve the issue by making the necessary code changes in the respective repositories.
Use @workspace as context.
If the issue requires changes in multiple repositories, make sure to create separate branches for each repository following the defined naming conventions.
Create test cases to validate the changes made and ensure that they do not introduce any regressions.
Create a detailed commit message that describes the changes made and the issue resolved.
Ensure that all quality checks and tests pass before committing the changes.
Ensure that all security and compliance requirements are met while making the code changes.
Ensure that all architectural and design guidelines are followed while making the code changes.
Stage all changes and commit.
Do NOT push.
Do not create a PR.
Never merge the PR.
