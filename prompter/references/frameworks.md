# Prompting Frameworks Reference

Most prompts work fine with the default XML or markdown templates in `SKILL.md`. This reference documents named frameworks that are worth reaching for when they genuinely fit the task — and when they don't. Use this file when the user asks for a specific framework by name, or when the task type strongly matches a framework's shape.

## Quick selection guide

| Task type | Best fit | Why |
|---|---|---|
| Content for an audience (blog, email, ad copy) | CO-STAR | Audience, style, tone are first-class |
| Multi-step execution with constraints | RISEN | Sequencing + narrowing are first-class |
| Simple one-shot tasks | RTF | Minimal ceremony, fast to write |
| Behaviour-constrained workflows (code style, formats) | TIDD-EC | Do/Don't lists earn their keep |
| Reasoning-heavy tasks (math, logic, analysis) | CoT | Step-by-step thinking is the deliverable |
| Iterative summarisation | CoD | The whole point is iterative compression |
| Exploration, brainstorm, thinking out loud | None — free-form | Frameworks suppress the openness |
| Anything for Claude / Claude Code | XML-tagged (any framework) | Claude attends to XML structure strongly |

When in doubt, pick the simplest framework that contains every section the task actually needs.

---

## CO-STAR

**Stands for:** Context, Objective, Style, Tone, Audience, Response.

**Best for:** Content creation where audience and voice are load-bearing — blog posts, marketing copy, internal comms, customer-facing emails, scripts, social posts.

**Why it works:** Most content tasks fail because the writer (human or AI) didn't internalise the audience and tone. CO-STAR makes those fields impossible to skip.

**Template:**

```
**Context:** [Background the model needs]
**Objective:** [What success looks like in one sentence]
**Style:** [Genre or reference style — e.g., "punchy newsletter prose like Stratechery"]
**Tone:** [Emotional register — e.g., "confident but self-aware"]
**Audience:** [Who reads this — be specific about their context and expertise]
**Response format:** [Exact shape of the output]
```

**Skip CO-STAR when:** the task is execution-heavy (code, scripts, data work) and audience/tone don't apply.

---

## RISEN

**Stands for:** Role, Instructions, Steps, End-goal, Narrowing.

**Best for:** Execution tasks with a clear sequence and explicit constraints — building something, transforming data, running a defined process.

**Why it works:** The "Steps" section forces the user (and the model) to think about sequence rather than dumping a wishlist. "Narrowing" makes constraints explicit instead of hoping the model guesses.

**Template:**

```
**Role:** [Who the model is acting as — e.g., "a senior backend engineer"]
**Instructions:** [What to do, at a high level]
**Steps:**
1. [step]
2. [step]
3. [step]
**End-goal:** [The deliverable in concrete form]
**Narrowing:** [Constraints — what must be true, what must not be true]
```

**Skip RISEN when:** the task is exploratory (no clear steps) or trivially simple (steps would be padding).

---

## RTF

**Stands for:** Role, Task, Format.

**Best for:** Simple, one-shot tasks where ceremony slows you down — quick translations, short rewrites, one-off questions where the model needs minimal context.

**Why it works:** It contains the bare minimum needed for a coherent response — who the model is, what it should do, what shape the answer takes.

**Template:**

```
**Role:** [Who the model is acting as]
**Task:** [What to do, in one or two sentences]
**Format:** [The shape of the response]
```

**Skip RTF when:** the task has constraints, audience considerations, or steps that matter — RTF will be too thin.

---

## TIDD-EC

**Stands for:** Task, Instructions, Do, Don't, Examples, Context.

**Best for:** Repeatable workflows where consistency matters and the model has a habit of doing the wrong thing — coding tasks with style rules, structured data transformation, classification tasks, anything where Do/Don't lists exist for a reason.

**Why it works:** Explicit Do/Don't sections push back on the model's defaults. Examples ground the abstract instructions.

**Template:**

```
**Task:** [The job in one sentence]
**Instructions:** [How to approach it]
**Do:**
- [behaviour to encourage]
- [behaviour to encourage]
**Don't:**
- [behaviour to suppress]
- [behaviour to suppress]
**Examples:**
Input: [example input]
Output: [example output]
**Context:** [anything the model needs to know that doesn't fit above]
```

**Skip TIDD-EC when:** the task is one-off (the Examples section will feel forced) or open-ended (the Do/Don't lists become arbitrary).

---

## CoT (Chain of Thought)

**Stands for:** explicit instruction to reason step by step before answering.

**Best for:** Math problems, logic puzzles, multi-step analysis, debugging, anything where the final answer depends on intermediate reasoning being right.

**Why it works:** Asking the model to think out loud improves accuracy on reasoning-heavy tasks. For Claude specifically, you can ask it to use `<thinking>` tags so the reasoning is separable from the answer.

**Template (add to any other framework):**

```
[Your existing prompt structure]

Before giving your final answer, work through the problem step by step inside <thinking></thinking> tags. Then state your final answer clearly outside the tags.
```

**Skip CoT when:** the task is mostly recall, formatting, or creative — thinking out loud adds latency without adding quality.

---

## CoD (Chain of Density)

**Stands for:** an iterative summarisation pattern — produce a summary, then rewrite it denser, then denser again.

**Best for:** Producing high-information-density summaries from long sources where the first pass is usually too verbose.

**Why it works:** Each pass forces the model to drop weaker content and pack more signal per word.

**Template:**

```
Summarise the document below. Then produce four more increasingly dense rewrites of the summary, each shorter than the last, each preserving the most important information.

After each rewrite, list which entities or facts you removed and which you kept.

[Document]
```

**Skip CoD when:** you want a single output, not a process; CoD produces a chain of summaries which is overkill for most cases.

---

## XML tagging (Claude-specific)

Not a framework on its own — a structural choice that works inside any framework. Claude is trained to attend carefully to XML tags. For prompts targeting Claude or Claude Code, wrap each section of your prompt in semantically named tags:

```
<role>...</role>
<context>...</context>
<task>...</task>
<constraints>...</constraints>
<examples>
  <example>
    <input>...</input>
    <output>...</output>
  </example>
</examples>
<output_format>...</output_format>
```

Tag names can be anything that describes the content semantically — `<persona>`, `<rules>`, `<reference_material>` all work. The tags help Claude parse complex prompts, and they make it easier to reuse and edit a prompt later.

For non-Claude targets, swap XML tags for markdown headers. Same content, slightly less structural emphasis.

---

## When to combine frameworks

Frameworks aren't exclusive. Common combinations:

- **CO-STAR + CoT** — content with reasoning behind it (e.g., a strategic memo with explicit analysis)
- **RISEN + TIDD-EC** — execution with strong behavioural constraints (e.g., code generation with style rules)
- **Any framework + XML tags** — when targeting Claude, wrap sections in tags regardless of which framework you picked

Avoid combining frameworks that have overlapping sections (e.g., CO-STAR and RTF both have a Role-equivalent — pick one). The goal is clarity, not maximalism.