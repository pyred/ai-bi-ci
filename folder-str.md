For GitHub Copilot, I recommend keeping all repository-level AI configuration under a `.github/` directory at the root of your repository.

GitHub Copilot supports several types of customization files, each serving a different purpose:

- Instructions – Tell Copilot how your codebase is structured and how you want code written.
- Agents – Define specialized AI personas for particular responsibilities.
- Skills – Give Copilot reusable knowledge and workflows for specific tasks.
- Prompts – Store reusable prompts for common development activities.
- Hooks – Define actions that run at particular points in supported agent workflows.

## 1. Recommended folder structure

Here is the structure I'd use for a medium-to-large software project:

text

```text
your-repository/
│
├── .github/
│   │
│   ├── copilot-instructions.md
│   │
│   ├── instructions/
│   │   ├── architecture.instructions.md
│   │   ├── coding-standards.instructions.md
│   │   ├── security.instructions.md
│   │   ├── testing.instructions.md
│   │   ├── frontend.instructions.md
│   │   ├── backend.instructions.md
│   │   └── database.instructions.md
│   │
│   ├── agents/
│   │   ├── architect.agent.md
│   │   ├── code-reviewer.agent.md
│   │   ├── security-reviewer.agent.md
│   │   ├── test-engineer.agent.md
│   │   └── refactoring.agent.md
│   │
│   ├── skills/
│   │   ├── analyze-codebase/
│   │   │   ├── SKILL.md
│   │   │   └── reference/
│   │   │       └── architecture-checklist.md
│   │   │
│   │   ├── create-api/
│   │   │   ├── SKILL.md
│   │   │   └── templates/
│   │   │
│   │   ├── code-review/
│   │   │   └── SKILL.md
│   │   │
│   │   ├── database-migrations/
│   │   │   └── SKILL.md
│   │   │
│   │   └── debugging/
│   │       └── SKILL.md
│   │
│   ├── prompts/
│   │   ├── analyze-architecture.prompt.md
│   │   ├── review-code.prompt.md
│   │   ├── generate-tests.prompt.md
│   │   └── explain-code.prompt.md
│   │
│   └── hooks/
│       └── hooks.json
│
├── docs/
│   ├── architecture/
│   │   ├── overview.md
│   │   ├── system-design.md
│   │   └── diagrams/
│   │
│   ├── api/
│   ├── development/
│   └── decisions/
│
├── src/
│   └── ...
│
├── tests/
│   └── ...
│
└── AGENTS.md
```

This uses the current GitHub Copilot customization conventions. Some capabilities vary depending on whether you're using VS Code, Visual Studio, Copilot CLI, or GitHub.com.

GitHub Docs

+2

## 2. What each folder is responsible for

### A. `copilot-instructions.md` — Global repository instructions

Automatically appliedThis is probably the most important file to create first.

Location:

text

```text
.github/copilot-instructions.md
```

Use it to explain:

- What the application does.
- The overall architecture.
- Languages and frameworks.
- Important directories.
- Coding conventions.
- Build and deployment processes.
- How to run tests.
- General expectations when modifying code.

Example:

markdown

```markdown
# Repository Instructions

## Project Overview
This application is a .NET backend with a React frontend.

## Architecture
- Follow clean architecture principles.
- Keep business logic separate from infrastructure.
- Use dependency injection.
- Follow existing design patterns.

## Development Guidelines
- Follow existing naming conventions.
- Prefer small, focused changes.
- Avoid unnecessary dependencies.
- Write unit tests for new business logic.

## Validation
- Run the relevant unit tests.
- Run the build before completing changes.
- Do not introduce breaking API changes without approval.

## Important
Before modifying code:
1. Understand the existing implementation.
2. Identify affected components.
3. Follow established architectural patterns.
4. Explain significant design decisions.
```

Think of this as the general operating manual for Copilot.

### B. `instructions/` — Context-specific coding instructions

Automatically applied when applicableThis is where you put rules that only apply to certain parts of your codebase.

For example:

`.github/instructions/backend.instructions.md`

markdown

```markdown
---
applyTo: "src/backend/**/*.cs"
---

# Backend Development Rules

- Follow SOLID principles.
- Use dependency injection.
- Keep controllers thin.
- Business logic belongs in services.
- Use async/await for I/O operations.
- Use structured logging.
```

Another example:

`.github/instructions/frontend.instructions.md`

markdown

```markdown
---
applyTo: "src/frontend/**/*.{ts,tsx}"
---

# Frontend Development Rules

- Use functional React components.
- Prefer reusable components.
- Use TypeScript strict typing.
- Keep API calls in service modules.
- Follow existing state management patterns.
```

The important distinction is that `applyTo` tells Copilot which files these instructions apply to.

You can have as many instruction files as you need.

GitHub Docs

+1

### C. `agents/` — Specialized AI agents

Selectable specialist agentsAgents represent different roles or areas of expertise.

For example:

| Agent | Responsibility |
| --- | --- |
| Architect | Architecture analysis and design |
| Code Reviewer | Reviews code quality |
| Security Reviewer | Identifies security weaknesses |
| Test Engineer | Designs and creates tests |
| Refactoring Agent | Improves existing implementations |

Example:

`.github/agents/architect.agent.md`

markdown

```markdown
---
name: Architect
description: Analyzes the codebase architecture and recommends improvements.
---

# Role

You are a senior software architect.

# Responsibilities

- Analyze the existing architecture.
- Identify architectural patterns.
- Identify coupling and dependencies.
- Detect design inconsistencies.
- Recommend improvements.

# Rules

- Do not assume architecture based on folder names.
- Inspect actual implementations.
- Reference specific files and classes.
- Explain tradeoffs.
- Do not modify code unless explicitly requested.

# Output

1. Architecture overview
2. Main components
3. Dependencies
4. Architectural concerns
5. Recommendations
```

You can then select the Architect agent from the Copilot agent selector in supported IDEs.

Note that agent files use the `.agent.md` convention in VS Code.

GitHub Docs

+1

### D. `skills/` — Reusable specialized knowledge and workflows

Loaded when relevantThis is particularly useful because you don't want Copilot loading every detailed instruction into every conversation.

A skill is a directory containing a mandatory `SKILL.md` file and optional supporting resources.

Example:

text

```text
.github/skills/analyze-codebase/
│
├── SKILL.md
├── reference/
│   ├── architecture-checklist.md
│   ├── dependency-analysis.md
│   └── code-quality-checklist.md
│
└── scripts/
    └── dependency-report.sh
```

Example `SKILL.md`:

markdown

```markdown
---
name: analyze-codebase
description: Performs comprehensive codebase analysis,
  including architecture, dependencies, technical debt,
  design patterns and code quality.
---

# Codebase Analysis Workflow

When asked to analyze the codebase:

1. Identify the application entry points.
2. Map the major components.
3. Identify architectural boundaries.
4. Trace important dependencies.
5. Identify external integrations.
6. Examine error handling.
7. Evaluate test coverage.
8. Identify technical debt.

## Analysis Guidelines

- Inspect actual source code.
- Do not rely exclusively on documentation.
- Trace dependencies across projects.
- Identify concrete examples.
- Distinguish observations from assumptions.

## Expected Output

Produce a report containing:

1. Executive summary
2. Architecture overview
3. Component dependencies
4. Design patterns
5. Potential issues
6. Technical debt
7. Recommendations
```

The key benefit: Copilot can discover the skill from its description and load the detailed workflow when relevant.

Skills can also contain scripts, templates, examples, and reference documents.

GitHub Docs

+1

### E. `prompts/` — Reusable tasks

These are predefined prompts that you explicitly invoke.

For example:

`.github/prompts/analyze-architecture.prompt.md`

markdown

```markdown
---
description: Analyze the architecture of the current codebase.
---

Analyze the architecture of this repository.

Identify:

1. Main architectural patterns.
2. Application boundaries.
3. Component responsibilities.
4. Dependencies.
5. Potential architectural problems.
6. Opportunities for improvement.

Reference actual files and classes.
Do not modify any code.

Provide a structured report.
```

Useful for recurring activities such as:

- Analyzing architecture.
- Reviewing pull requests.
- Generating unit tests.
- Explaining complex implementations.
- Finding technical debt.

Unlike instructions, prompts are explicitly invoked rather than automatically applied.

GitHub Docs

## 3. Understanding when Copilot uses each type

This is probably the most important distinction when designing your configuration.

| Type | How Copilot uses it |
| --- | --- |
| Global instructions | Automatically |
| Path-specific instructions | Automatically when applicable |
| Agents | You select a specialist agent |
| Skills | Automatically discovered when relevant |
| Prompts | You explicitly invoke them |
| Hooks | Execute at configured workflow events |
| MCP | Provides access to external tools and systems |

GitHub Docs

+1

## 4. My recommended approach for a serious codebase

I would avoid putting everything into one enormous instruction file.

Instead, I'd organize things into three levels:

Level 1 — General knowledge

Always available where applicable

- `copilot-instructions.md`
- `architecture.instructions.md`
- `coding-standards.instructions.md`
- `security.instructions.md`

Level 2 — Specialized expertise

Loaded or selected when needed

- Architecture Agent
- Security Agent
- Code Review Agent
- Codebase Analysis Skill
- Database Migration Skill

Level 3 — Repeatable workflows

Invoked for specific activities

- Architecture analysis prompts
- Code review prompts
- Test generation prompts
- Build and validation hooks

## 5. A few important things to know

- Don't duplicate instructions. Put general rules in the global file and specialized rules in their respective files.
- Keep skills focused. One skill should ideally accomplish one specific type of task.
- Use `AGENTS.md` where appropriate. You can put these files in subdirectories to give agents closer-to-the-codebase context. The nearest applicable file takes precedence in supported Copilot environments.
  GitHub Docs
  +1
- Document your architecture. Copilot will produce much better results when it can reference actual architecture diagrams, ADRs, API contracts, and technical documentation.
- Verify feature support in your IDE. Not every customization type works identically across all Copilot clients.

## 6\. Official GitHub documentation

These are worth bookmarking:

- [GitHub Copilot customization cheat sheet](<https://docs.github.com/en/copilot/reference/customization-cheat-sheet?utm_source=chatgpt.com>) — Excellent overview of all customization types.
- [Repository custom instructions](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions?ref=apidog.com&utm_source=chatgpt.com>).
- [Creating agent skills](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills?hl=en-US&utm_source=chatgpt.com>).
- [Creating custom agents](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents-in-your-ide?tool=vscode&utm_source=chatgpt.com>).
- [Custom instructions and precedence](<https://docs.github.com/en/copilot/concepts/prompting-and-context/custom-instructions?utm_source=chatgpt.com>).

## 7\. One additional recommendation

I'd strongly consider creating a dedicated `analyze-codebase` skill and an `architect.agent.md` agent as your starting point.

Together, these would let you ask Copilot questions like:

- "Analyze the entire codebase and explain its architecture."
- "Identify all dependencies between projects."
- "Find areas with high coupling."
- "Identify inconsistencies with our architectural principles."
- "Evaluate whether our implementation follows SOLID."
- "Identify opportunities to reduce technical debt."

The skill defines how to perform the analysis, while the agent defines the expertise and responsibilities of the architect.

That separation makes the configuration much easier to maintain as your codebase grows.
