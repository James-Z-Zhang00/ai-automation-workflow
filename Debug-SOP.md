# Debug SOP

### Tips
1. **Feed the Traceback:** Never describe the error in your own words if you can copy-paste the raw stack trace.
2. **Scope the Environment:** Tell the tool which files are relevant so it doesn't waste tokens reading your entire node_modules.
3. **The "Verify" Step:** Ask the AI to output (a) a minimal reproduction input, (b) an assertion, and (c) the exact command to run.
4. **Stop rule:** If we’ve tried 2 hypotheses without new evidence, pause and gather new logs/instrumentation before asking again.

==**&nbsp;Use Markdown or XML tags to help LLM understand the prompt, the structure matters!&nbsp;**==

### BUG REPORT & CONTEXT
**1. The Problem:** I am seeing [Describe behavior, e.g., "a 500 error on login"] when I [Describe action, e.g., "click submit with an empty password"].

**2. The Traceback/Logs:** [PASTE RAW ERROR LOG OR STACK TRACE HERE]

**3. Relevant Files:** Please analyze:
- `path/to/file_a.ext` (The likely source)
- `path/to/file_b.ext` (The related component)

**4. Goal:** First, explain the root cause based on the code logic. 
Second, propose a minimal fix. 
Third, suggest a command to run a test that verifies the fix.

**5. Constraints:** Do not refactor; patch must be `N lines; keep public API unchanged.` (or whatever constraint applies)`

**Do not apply changes until I confirm.**

### Claude Commands

| Command | Description | When to Use |
| :--- | :--- | :--- |
| **`/compact`** | **The Token Saver.** Summarizes chat history to free up context window. | Use when context gets noisy or the assistant forgets constraints. |
| **`/cost`** | **The Wallet Check.** Shows current session cost & token usage. | Before starting a complex refactor or large file analysis. |
| **`/clear`** | **The Hard Reset.** Wipes the session memory completely. | If the AI gets stuck in a logic loop or "hallucination spiral." |
| **`/add [file]`** | **The Sniper.** Adds a specific file to context. | Instead of letting it read the whole repo, force it to look here. |
| **`/remove [file]`** | **The Cleanup.** Removes a file from context. | When switching tasks (e.g., from Backend to Frontend). |
| **`/init`** | **The Brain.** Creates a `CLAUDE.md` rule file. | Run once per project to set permanent rules (e.g., "Always use TypeScript"). |

> **🔥 Pro-Tip: The "Manual Reset" (Better than `/compact`)**
> 1. Ask Claude: *"Write a `plan.md` summary of our progress and next steps."*
> 2. Run `/clear` to wipe the memory.
> 3. Run `/add plan.md` to restore *only* the clean state.

### 🔍 DATA PIPELINE DIAGNOSTIC

```xml
<objective>
Fix the extraction logic for the [e.g., "Risk Factors"] section in an SEC 10-K filing.
</objective>

<pipeline_logic>
[Paste the specific function/regex you are using to find the section]
</pipeline_logic>

<source_sample>
</source_sample>

<the_failure>
- **Expected:** Parser identifies header at line X.
- **Actual:** [e.g., "Parser skips to the end of file" or "Returns empty string"]
</the_failure>

<task>
1. Analyze the <source_sample> for hidden HTML tags or encoding issues.
2. Provide a robust regex or logic fix that accounts for SEC filing inconsistencies.
3. Write a small test script using the <source_sample> to verify.
</task>
```

### Sample Prompt & Commands

```text
"Claude, I'm going to provide a snippet of an SEC filing. I want you to act as a string debugger. Identify every invisible character, nested <span>, or <b> tag that might be breaking my match("Item 1A") logic. Then, suggest a 'normalization' function to run before parsing."
```

Use `/grep` inside Claude Code to find other instances of the header in your local data cache: 🚀 `/grep -C 5 "Item 1A" ./data/sec_files/`(This shows the 5 lines of context around the header to help you see the pattern.)

```
## Phase 1: Context Initialization (0-5 min)
* **Isolate the Bug:** Reproduce the error locally.
* **Gather Artifacts:** Collect the error log, the failing test output, and the relevant file paths.
* **Initiate Tool:** Launch `claude-code` or your preferred AI agent in the project root.
* **Initial Prompt:** > "I am seeing [Error Name] in [Component/Module]. Here is the traceback: [Paste Traceback]. Please read [filepath/A] and [filepath/B] to understand the flow."

## Phase 2: Diagnostic Analysis (5-15 min)
* **Hypothesis Generation:** Ask: "What are the 3 most likely causes for this behavior?"
* **Code Exploration:** Use the AI to check state or variable flow.
    * *Command:* "Trace the data flow of `user_id` from the API entry point to the database save."
* **Avoid Assumptions:** If the AI suggests a fix, ask: "What evidence in the code confirms this is the root cause?"

## Phase 3: The Fix-Verify Loop (15-25 min)
* **Draft a Test:** Before applying a fix, have the AI write a minimal reproduction script or unit test.
* **Apply Fix:** Let the AI apply the change. 
    * *Note:* If using Claude Code, use `/undo` if the change breaks unrelated features.
* **Regression Check:** Run the full test suite. Ask: "Are there any side effects of this change in [Related Module]?"

## Phase 4: Documentation & Cleanup (25-30 min)
* **Refactor:** Ask the AI to simplify the fix if it looks "hacky."
* **Summarize:** Ask the AI: "Explain why this bug happened and how the fix addresses it." 
* **Log:** Save this summary in your PR description or internal wiki.

### BUG REPORT & CONTEXT
**1. The Problem:** I am seeing [Describe behavior, e.g., "a 500 error on login"] when I [Describe action, e.g., "click submit with an empty password"].

**2. The Traceback/Logs:** [PASTE RAW ERROR LOG OR STACK TRACE HERE]

**3. Relevant Files:** Please analyze:
- `path/to/file_a.ext` (The likely source)
- `path/to/file_b.ext` (The related component)

**4. Goal:** First, explain the root cause based on the code logic. 
Second, propose a minimal fix. 
Third, suggest a command to run a test that verifies the fix.

**Do not apply changes until I confirm.**
  

# 🎯 BUG DIAGNOSIS

<context>
- **Goal:** Fix [Error Name]
- **Files:** [path/to/file_A], [path/to/file_B]
</context>

<error_logs>
[PASTE TERMINAL OUTPUT OR STACK TRACE HERE]
</error_logs>

<constraints>
- Use existing project design patterns.
- Do not add new dependencies.
- **STOP and explain your plan** before editing files.
</constraints>

**Claude, analyze the logs against the context and propose a resolution.**

## 💎 Token & Time Efficiency
* **Limit Scope:** Only provide code relevant to the stack trace. 
* **XML Tags:** Use `<error>`, `<code>`, and `<goal>` to help Claude's "Attention Mechanism" focus, reducing compute time.
* **Control Output:** Use "Diff only" or "Short explanation" to save generation tokens.
* **Session Reset:** Use `/compact` every 10-15 minutes to clear out-of-date context.
```