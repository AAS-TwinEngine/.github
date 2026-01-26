# Contributing Guide – AAS.TwinEngine

Thank you for contributing! We aim for transparent, traceable collaboration across initiatives, demands, and releases.

---

## 1. Code of Conduct
Please read and follow:
https://github.com/AAS-TwinEngine/.github/blob/main/CODE_OF_CONDUCT.md

---

## 2. Developer Certificate of Origin (DCO)
Every commit MUST include a sign-off line:
Signed-off-by: Full Name <email@example.com>

Add it automatically:
git commit -s -m "Your commit message"

More info: https://developercertificate.org/

---

## 3. Branching & Pull Requests
### Branch Structure

A standard branch structure is as follows:

• develop – Ongoing development and integration  
• feature/* – Feature-specific branches  

---

### Branching Strategy

#### Develop Branch

• Used for active development  
• Feature branches are merged into this branch  

---

#### Feature Branches

• Created from develop  
• Naming convention: feature/{ticket-id}-{short-description}  
• Used for individual features or enhancements  
• Deleted after successful merge  

---

### Release Management

#### Feature Development

1. Create feature branch from develop:  
   `git checkout -b feature/TICKET-123-new-auth`

2. Develop and commit changes  

3. Push branch: triggers Docker build with tag  
   `feature-{short-sha}`  

4. Create Pull Request to develop with title:  
   `[TICKET-123] Brief description`  

5. Manual Approval Required: All PRs require manual approval  

6. QA Approval Required: If "Testcase required" is set to true, QA engineer approval is mandatory  

7. Squash and merge to develop  

8. Automatic cleanup of feature branch and images 

---

## 4. Testing & Documentation
- Update tests and docs for any changes
- Follow repository-specific style and linting rules

---

## 5. Security
Do NOT disclose vulnerabilities publicly.
Report via:
https://github.com/AAS-TwinEngine/.github/blob/main/SECURITY.md

---

## 6. Discussions
Use GitHub Discussions for design proposals and link them in PRs for traceability.

---

Thank you for helping us build a robust, transparent ecosystem!
