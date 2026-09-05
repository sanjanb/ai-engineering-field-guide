## Programming with AI (Some good tips that may help :D)
- Learn to program first!!! (AI can multiply your skill)
- Be specific as possible with AI (as programmers are not good at communication :D), be more detailed and ideal (Cover all the technical aspects, techstack, architectre, rules, flow, give resources that would help to get good context)
- Plan out the solution, break the down the problem down into smaller pieces (First-Principle Thinking), Code it



## Best practices for Terminal Native Coding Agents (TNCAs):

| Category | Technique / Tool | Key Concept & Best Practice |
| --- | --- | --- |
| **Planning & Architecture** | **Plan Mode** (`Shift + Tab`) | Forces the agent to thoroughly read code and generate a plan before executing edits. **Tip:** Pair a smart model for planning with a cheaper, faster model for implementation. |
| | **[Improve Skill](https://github.com/shadcn/improve)** | Audits the codebase and generates structured implementation plans for downstream agents to execute. |
| | **[Improve Codebase Architecture](https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md)** | Guides structural refactoring and architectural improvements across the codebase. |
| **Skills & Anti-Slop** | **Verbalized Skills** ([skills.sh](https://www.skills.sh/), [Matt Pocock Skills](https://github.com/mattpocock/skills)) | Standardized `skill.md` markdown files that provide repeatable engineering guidelines and project patterns. |
| | **[Thermonuclear Quality Review](https://github.com/cursor/plugins/blob/main/cursor-team-kit/skills/thermo-nuclear-code-quality-review/SKILL.md)** | Strict review skill focused on simplifying agent output and stripping unnecessary AI slop. |
| | **[Ponytail](https://github.com/DietrichGebert/ponytail)** | Enforces minimal code footprints and clean coding principles to accomplish tasks in as few lines as possible. |
| **Verification & Testing** | **Pre-Implementation Verification** | Require tests to be written *first* before feature implementation. Focus on core business logic rather than 100% line coverage, and mandate linters, type-checkers, or browser/screenshot testing. |
| **Context & Token Optimization** | **Session Isolation & "Dumb Zone"** | Agent performance dips significantly beyond ~100k tokens. Start a fresh session for every distinct task. |
| | **Targeted Prompting** | Pass explicit file paths and hyper-specific instructions to stop agents from going on expensive research tangents. |
| | **Manual Compression (`/compact`)** | Execute `/compact` manually before automatic mid-task triggers cause context quality drops. |
| | **[CAVEMAN](https://github.com/JuliusBrussee/caveman)** | Reduces agent output verbosity to maximize remaining context space. |
| | **[RTK](https://github.com/rtk-ai/rtk)** | Minimizes context/token overhead generated when agents run terminal commands and parse massive output logs. |
| **Integrations & Protocols** | **MCP Best Practices** | Use Model Context Protocols exclusively when interacting *outside* the codebase (databases, external docs, browsers). Avoid installing excessive MCPs. |
| **Utilities & Commands** | **Native Commands** | • `/voice`: Input complex prompts faster by speaking.<br>• `/btw`: Ask side questions mid-task without corrupting chat history.<br>• `/teleport` / `/remote`: Move live sessions between terminal, web, and mobile.<br>• `!` *(Shell Mode)*: Run terminal commands directly while maintaining agent context visibility.<br>• `/radio`: Terminal lo-fi player for background music. |
| **Automation & Parallelism** | **`/loop` Command** | Runs cron-like recurring jobs (e.g., auto-resolving open GitHub issues, daily security scans). |
| | **`/goal` Command** | Continuously loops until a specific outcome is achieved (token-intensive; best suited for higher-tier accounts). |
| | **Sub-Agents** | Spawns specialized mini-agents (researcher, debugger) with isolated context windows (drains tokens rapidly on $20 plans). |
| | **Git Worktrees** | Isolates parallel agent sessions across separate Git branches to prevent file write conflicts. |
