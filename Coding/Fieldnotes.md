---
tags: DevOps
---
A project in my [[DevOps]] class

### Demo login
Username: `demo@fieldnotes.test`
Password:  `fieldnotes-demo`

### Runbook Variables

```bash
GITLAB_USER=davidra
PROJECT_PATH="andrews-university/courses/fall2026-cptr320/projects/project-01-fieldnote-david.randall" # from your Project 1 Container Registry page, or andrews-university/courses/fall2026-cptr320/topics/topic-06-composing-the-stack-services-networks-and-the-database-tier for the prebuilt images
PROJECT_DIR="$HOME/cs320/project-01-fieldnote-david.randall" # your Project 1 checkout
LESSON_DIR="$HOME/cs320/in-class/topic-06-composing-the-stack-services-networks-and-the-database-tier" # where this lesson repository is cloned
LESSON_REPO_URL="https://gitlab.au-computing.org/andrews-university/courses/fall2026-cptr320/topics/topic-06-composing-the-stack-services-networks-and-the-database-tier/-/tree/main?ref_type=heads"
REGISTRY=gitlab.au-computing.org:5050
```

Use:
```bash
PROJECT_PATH="andrews-university/courses/fall2026-cptr320/topics/topic-06-composing-the-stack-services-networks-and-the-database-tier"
```
for his prebuilt images

