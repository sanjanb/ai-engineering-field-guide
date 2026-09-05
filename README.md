## Programming with AI (Some good tips that may help :D)
- Learn to program first!!! (AI can multiply your skill)
- Be specific as possible with AI (as programmers are not good at communication :D), be more detailed and ideal (Cover all the technical aspects, techstack, architectre, rules, flow, give resources that would help to get good context)
- Plan out the solution, break the down the problem down into smaller pieces (First-Principle Thinking), Code it
- Do not let AI do all the thinking for you, you do the thinking part, let the AI do the coding part
- Tell AI what you don't want (very important :D, use DO NOT Section in a md file)
- Tell AI to remember ie, GUIDELINES.md, RULES.md.
- Use [MCP's](https://mcpmarket.com/server) to make your life easier like context7, nextjs dev tools mcps, chrome dev tools,
- Give AI access to verify the code itself

## Principles & Best Practices for AI-Assisted Programming

### Mindset & Core Principles
* **Learn to Code First:** AI is a force multiplier for existing skill, not a replacement for domain knowledge. If you cannot evaluate the output, you cannot direct the model effectively.
* **You Architect, AI Executes:** Retain ownership of the problem-solving and architectural design. Define the logic and boundary conditions yourself—delegate the repetitive coding and boilerplate implementation to the AI.

### Context, Rules & Constraints
* **Be Hyper-Specific:** Detail your exact technical requirements, tech stack, data flows, and edge cases. Avoid vague descriptions; clarity in prompts dictates output quality.
* **Define Negative Constraints ("DO NOT" List):** Explicitly tell the AI what **not** to do. Include a dedicated `DO NOT` section in your rules file to eliminate common AI slop and anti-patterns.
* **Maintain Persistent Guidelines:** Use standardized context files (`RULES.md`, `GUIDELINES.md`, or `CLAUDE.md`) to enforce coding standards, file structures, and naming conventions automatically across sessions.

### Workflow & Tooling
* **Apply First-Principles Planning:** Break complex features down into small, modular sub-tasks before invoking code generation. Plan out the entire flow before writing line one.
* **Automate Self-Verification:** Provide the AI with tool access to run tests, linters, type checkers, or browser instances so it can verify its own code before marking a task complete.
* **Integrate MCPs (Model Context Protocol):** Connect your agent directly to your dev environment using [MCP Servers](https://mcpmarket.com/server) (e.g., Next.js devtools, Chrome DevTools, database inspectors) to stream real-time operational context.


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
