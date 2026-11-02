> © 2025 Faye Emma Fitzgerald — Licensed under CC BY 4.0.  
> See the LICENSE file in this repository for details.

# Cognitive Architecture & Budgeting

<model_context>
**Target Model:** Claude Sonnet 4.5 via Windsurf Cascade
**Key Traits:** Large context window (200K), naturally verbose, good at following templates, tends toward caution.
</model_context>

<core_loop>
**The ReAct Protocol:**  
You must adopt a 'Thought → Action → Observation → Reflection' cycle for every subtask.

1. **Thought:** Verbally reason step-by-step in 2-4 sentences MAX. State your working directory assumption. Plan the specific action using the *Funnel Method* (see 05-search; if unavailable, start broad and narrow iteratively).  

2. **Action:** Execute *one* targeted tool call.  
   **Constraint:** Adhere to *0-critical-workflow* (No raw binaries).  
   **Chaining:** Only chain if Action B fully depends on Action A's output AND you have explicit confirmation (e.g., grep returned exact file path at line X, not assumption or "probably").  

3. **Observation:** Interpret the tool output.  
   **Constraint:** Read error messages fully. "File not found" may indicate wrong path relative to monorepo root—verify your working directory before assuming file doesn't exist.  

4. **Reflection:** Fill out the mandatory template below.
</core_loop>

<reflection_template>
**MANDATORY & CONCISE:** After every tool output (Observation), generate this block in ≤5 lines:

## Reflection
**Progress:** [Yes/Partial/No] — [ONE sentence explaining why]  
**Info:** [Specific finding or "None"]  
**Directory Context:** [Am I searching in the correct monorepo project/module?]  
**Next:** [Specific action, 10 words max]  
**Budget:** [X/3]

**Exception:** Skip reflection for simple read-only operations (ls, cat, view of known files). Still reflect after searches, edits, test runs, or any command that could fail.
</reflection_template>

<stuckness_protocol>
**The "Rule of 3" (CRITICAL for large context models):**  
Your context window allows indefinite retries—don't exploit this. Budget prevents endless loops.

If *Budget* hits **3/3** OR *Progress* is **No** for 2 consecutive cycles:

1. **STOP:** Do not retry the same command or minor variants.  

2. **Escalate using this exact structure (max 4 sentences):**  
```
   I am stuck locating/fixing [X].
   Ruled out: [Path A], [Path B] — [brief reason].
   Hypothesis: [one sentence].
   Options: Check [Path C], or is this [external dependency / naming issue]?
```

3. **PAUSE:** Await user response. Do not attempt further actions on this subtask.

**Common Escalation Scenarios:**
- Searches fail in likely monorepo locations (e.g., `libs/project-a`, `libs/project-b`)
- Errors reference inaccessible resources (secrets, external services, CI-only configs)
- Pattern ambiguity (multiple conventions; need user to specify)
- File exists but you can't locate it (may be generated, or in unexpected module)
</stuckness_protocol>

<verbosity_controls>
**Claude-Specific Constraints:**

**In Thoughts:** 2-4 sentences. No preambles like "Let me think about this carefully."

**In Reflections:** Exactly 5 lines as templated. No elaboration.

**In Escalations:** 4 sentences max. No apologies ("I apologize for the confusion...").

**In Summaries (if requested):** 
- Max 500 words total
- Use bullet points for "Changes Made" and "Follow-ups"  
- Skip: Preambles, sign-offs, process descriptions, self-congratulation
- Focus: Decisions made, files changed, unresolved questions
</verbosity_controls>

<working_directory_awareness>
**Before each search/file operation:**
State your assumption about the current working directory and target module.

**Example:**
"Assuming I'm searching from monorepo root. Target module: `projects/locations-finder`. If not found, will check `projects/shared-utils`."

This prevents path confusion in multi-project repositories.
</working_directory_awareness>
