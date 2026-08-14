---
name: ResolveLocalIssue
description: Resolves the local draft created Issue in PM repository, commits the changes, and creates a draft PR.
agent: agent
---

Assign the draft issue in PM repository which can be located in PM .cache folder to Copilot and resolve the issue by making the necessary code changes in the respective repositories.
Start with tasks if there are any, continue with PBI's and then with Features.
Use @workspace as context.
If the issue requires changes in multiple repositories, make sure to create separate branches for each repository following the defined naming conventions.
Create test cases to validate the changes made and ensure that they do not introduce any regressions.
Create a detailed commit message that describes the changes made and the issue resolved.
If issue is PBI and has Task subissues, then stage all changes and make a commit for every Task subissue before resolving the PBI issue.
If issue is Feature and has PBI subissues, then stage all changes and make a commit for every PBI subissue before resolving the Feature issue.
If issue is Epic and has Feature subissues, then stage all changes and make a commit for every Feature subissue before resolving the Epic issue.
Ensure that all quality checks and tests pass before committing the changes.
Ensure that all security and compliance requirements are met while making the code changes.
Ensure that all architectural and design guidelines are followed while making the code changes.
Always stage all changes before committing and always commit with a message which follows the defined conventions.
Do NOT push.
Create a local draft PR.
Use the Pull Request Template.
In Pull Request Information add the number and description of the issue which was resolved with a link to the issue.
Never merge the PR.
