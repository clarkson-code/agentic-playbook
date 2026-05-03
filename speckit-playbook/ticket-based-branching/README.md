# Constitution Amendment — Ticket-Based Branching
 
## What this does
 
Amends a fresh SpecKit constitution to enforce ticket-based branch naming, nested spec directories, and safe branch reuse — replacing SpecKit's default sequential numbering (`001-feature-name`).
 
## Prerequisites
 
- Git repo initialised (`git init`)
- SpecKit initialised (`/speckit-init`)
- A constitution must exist at `.specify/memory/` before applying this amendment
## When to apply
 
Run this as the first amendment on any new SpecKit project, immediately after `/speckit-init`.
 
## How to apply
 
Copy the entire contents of [`CONSTITUTION_PROMPT.md`](./CONSTITUTION_PROMPT.md) and run it in your Claude session:
 
```
/speckit-constitution {paste contents here}
```
 
Then verify it saved:
 
```bash
cat .specify/memory/*.md | grep -n "allow-existing\|GIT_BRANCH_NAME\|feature.json"
```
 
If any references are missing, run it again — occasionally Claude saves a partial amendment.
 
## Folder structure
 
```
specs/
├── TEST-01/
│   ├── create-home-page/
│   │   └── spec.md
│   └── create-nav-bar/
│       └── spec.md
└── TEST-02/
    └── create-about-us-page/
        └── spec.md
```
 
One branch per ticket. Multiple specs nested under the same ticket. No sequential numbering.
 
## What it adds
 
### Agent Behaviour section
 
Rules that Claude follows on every `/speckit-specify` invocation:
 
| Rule | Behaviour |
|---|---|
| Ticket ID gate | Always asks for a ticket ID — never infers it |
| Ticket ID format | Rejects bare numbers (`001`) — must have a prefix (`TEST-1`) |
| Branch format | Branch named exactly `{ticket-id}` — no prefix, no suffix |
| Branch resolution | Reads live git state — never stale `.specify/feature.json` |
| Branch reuse | If already on the ticket branch, reuses it |
| New branch guard | If not on main, stops and instructs user to switch first |
| `GIT_BRANCH_NAME` scope | Always inline — never exported |
| Spec directory | Nested `specs/{ticket-id}/{kebab-summary}/` |
| Confirmation | Echoes resolved values and waits for explicit confirmation |
 
## Branch resolution rules
 
| Scenario | Behaviour |
|---|---|
| On `TEST-1`, provide `TEST-1` | Reuses branch, creates new nested spec |
| On `TEST-1`, provide `TEST-2` | Stops — instructs user to switch to main first |
| On `main`, provide `TEST-2` | Creates `TEST-2` branch, creates nested spec |
| Provide `001` | Rejected — must have non-numeric prefix |
