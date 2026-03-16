# Design SOP

### 1. Workflow
- **Explore:** Use Claude to audit the current state of the repo.
- **Plan:** Use Plan Mode to brainstorm logic without modifying files.
- **Document:** Direct Claude to write a `DESIGN.md` before implementation.

### 2. Commands

| Command | Purpose in System Design |
| :--- | :--- |
| **`Shift + Tab` (x2)** |	**Plan Mode:** Research and plan without making file changes. |
| **`/init	Creates`** | `CLAUDE.md` to store architectural "Source of Truth" rules. |
| **`/compact`** |	Summarizes the long conversation to save tokens and maintain focus. |
| **`Option + T`** |	**Extended Thinking:** Triggers deeper reasoning for complex logic. |
| **`@path/to/file`** |	References specific files to give Claude precise context. |

### 3. Strategy (Use RAG as an example)

**Phase A: Codebase Audit**
Map out your current retrieval flow to identify integration points.

>Prompt: `Analyze the current RAG flow in @src/retriever.py. Create a Mermaid diagram showing the data flow from query to response.`

**Phase B: Logic Planning (The "Router")**
Stress-test the decision logic for your new live-API feature.

>Prompt: `I want to add a real-time API call. If the vector DB similarity score is below 0.7, trigger the API. Plan how this impacts latency and where the new 'Router' class should live.`

**Phase C: Lock-in the Specification**
AI context is temporary; files are permanent. Solidify the design.

>Prompt: `Based on our plan, create a SPEC.md that defines the Interface for the New Router and the JSON schema for the API tool call.`

### 4. Prompting Patterns

>[!TIP] **The Interviewer Pattern** "I want to design a multi-modal RAG. Before you suggest code, interview me with 5 questions about our scaling requirements and data types."

>[!IMPORTANT] **The Contrast Pattern** "Suggest two ways to implement the 'Agentic Reasoning' layer—one using LangGraph and one using vanilla Python. Compare them for maintainability."

**File-Targeted Editing:** "Now that we've designed the Router, implement it in src/agents/router.py, ensuring it inherits from BaseAgent in src/base.py."

### 5. Pro-Tips

**Maintain** `CLAUDE.md`: Use this file as "Permanent Memory" for your project. Store rules like "Always use async for external API calls" or "No heavy frameworks for the retrieval layer."

**Iterative Scaffolding:** Ask Claude to write the **Interface/Boilerplate** first. Review the structure, then ask it to "fill in the implementation."

**Test-Driven Design:** Tell Claude: "Write the unit tests for the RAG retriever based on our new design specs before writing the retriever itself."

### 6. Token Management

**A. Limit the Scope with @**
Don't let Claude guess where the information is. If you know your RAG logic is in one folder, tell it.

- **Bad:** "Plan how to add the live API to my RAG." (Claude will scan the whole repo).
- **Good:** "Plan the live API integration using ONLY `@src/retriever.py` and `@src/api_client.py` as context."

**B. The "No-Search" Instruction**
Claude Code often tries to search the web or your local docs. You can explicitly disable this in your prompt.

>"Let's plan the architecture. **Do not use the** `search` **tool or external web tools.** Use only the files I've provided and your internal knowledge of Python."

**C. The "Stop & Interview" Prompt**
Instead of letting Claude generate a 5-page plan (huge tokens), force it to be interactive.

- **Prompt:** "I want to add a reasoning layer. List the 3 most logical places to insert this in my current code, then **stop and ask me which one I prefer** before continuing."

**D. Use `/compact` Religiously**
If you’ve been planning for 15 minutes, your "context" contains every single thing said so far.
- Run `/compact` frequently. It summarizes the conversation and "forgets" the redundant parts, keeping your token usage per message much lower.