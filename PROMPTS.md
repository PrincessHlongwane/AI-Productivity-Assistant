# Prompt Engineering Notes

This document explains the prompt design behind each module in Deskline, since the project brief asks us to demonstrate prompt engineering skill, not just wire up an API call.

## General approach
Every module uses a **system prompt** (fixed instructions defining the AI's role, tone, and output format) combined with a **user prompt** (the person's specific input, structured with labelled fields rather than free text). This two-part structure is a core prompt engineering technique: it keeps the AI's behaviour consistent across many different inputs, instead of re-explaining the task every time.

Design principles applied throughout:
- **Role assignment** — every system prompt opens by telling the model what kind of assistant it is (e.g. "You are a workplace communication assistant").
- **Explicit output format** — each prompt states what structure the answer must take (subject line + body, or headings for overview/decisions/action items), instead of leaving format to chance.
- **Labelled inputs** — user prompts are built from form fields (recipient, tone, purpose, key points) rather than one paragraph, so the model always receives complete, structured context.
- **Scope constraints** — prompts tell the model what *not* to do (e.g. "do not add explanations outside the email itself") to keep responses clean and directly usable.
- **Graceful defaults** — optional fields (like tone or deadline) fall back to sensible instructions such as "use your judgement" so the module still works with minimal input.

## Module 01 — Email Drafting
**System prompt:** defines the AI as a workplace communication assistant and fixes the output shape (subject line, greeting, body, sign-off).
**Why:** without a fixed shape, email length and structure vary wildly between requests. Locking the format makes output predictable and ready to copy into an email client.

## Module 02 — Meeting Summarization
**System prompt:** instructs a four-part structure — overview, key discussion points, decisions, action items (with owner/due date, or explicit "unassigned"/"no date given" placeholders).
**Why:** meeting notes are messy and unordered. Asking for named sections forces the model to extract structure rather than just paraphrase the notes, and the placeholder instruction stops the model from inventing owners or dates that weren't mentioned.

## Module 03 — Task Planner
**System prompt:** frames the AI as a task-planning assistant and asks for prioritized (High/Medium/Low), logically grouped tasks with a suggested order, explicitly asking the plan stay "practical, not overwhelming."
**Why:** without that last constraint, planning prompts tend to produce huge, unrealistic lists. Naming the failure mode directly in the prompt heads it off.

## Module 04 — Research Assistance
**System prompt:** asks for a summary, then bullet key points, then practical recommendations, with an instruction to flag when something is a general guideline rather than a strict rule.
**Why:** this reduces the risk of the assistant stating opinion or convention as fact — an important guardrail for a workplace tool.

## Module 05 — Assistant Chat
**System prompt:** short and general on purpose, since this module is the fallback for anything the other four don't cover. Conversation history is sent with every request so follow-ups stay in context — a basic but essential prompt engineering pattern for multi-turn chat.

## Reflections
The main lesson from building this was that **structure in the prompt produces structure in the output.** Vague prompts ("summarize this meeting") return inconsistent, unpredictable results. Naming the exact sections, format, and edge cases (missing dates, missing owners, optional fields) up front made the assistant's output usable without heavy editing — which is the actual goal of a workplace productivity tool.
