---
name: model-routing
description: Apply token-conscious GPT Codex model and reasoning selection. Use when the user invokes $model-routing or asks which model or effort to use for a task.
disable-model-invocation: true
---

# Token-Conscious Model Routing

Use the least expensive model and reasoning effort that can reliably complete the task.

At the beginning of the first response, briefly state the recommendation and reasoning effort, for example: `Recommendation: GPT-6.1 Sol at medium effort for this coding task.` Compare both model and effort with the active settings only when those settings are known; do not invent the active configuration.

When a change is recommended (or the active settings are unknown), show an interactive in-app approval prompt using `functions.request_user_input_async` when available. Ask: `Approve the recommended model setting: [model] at [effort]? This records your preference; it does not change the app's model selector.` Offer two choices: `Approve [model] · [effort]` and `Keep the current model`. Do not substitute a plain-text question when this UI tool is available. If it is unavailable, ask the same question in chat. Do not use shell escalation or execution-permission dialogs for model selection.

Wait for the user's answer before proceeding with the underlying task; an asynchronous tool's accepted result is not user approval. Keep the turn active while the asynchronous prompt is pending: do not send a final response or end the turn just to tell the user to answer. Use an available interruptible wait tool (for example, `clock.sleep` in intervals of at most 60 seconds), checking for the user's reply between waits. Do not reopen the same prompt, interpret elapsed time as approval, or proceed with dependent work without an answer. If no supported waiting mechanism is available, explain the limitation instead of claiming the UI will remain open. If the user chooses to keep the current model, continue with it. If they approve the recommendation, explain briefly that they must apply it in the app's model selector, and wait for confirmation that they changed it (or explicit instruction to continue with the current model). Do not claim approval changed the model or that a switch occurred without confirmation. This skill and rule request a model preference through the UI; they cannot perform the model change themselves. If both active settings already match, proceed without asking again.

- Small, exact edits or routine UI changes: GPT-6 Luna, low.
- Normal feature work, bug fixes, coordinated changes, and tests: GPT-6.1 Sol, medium.
- Ambiguous root causes, security, authentication, Redis/concurrency, Octane, architecture, or high-risk production behavior: GPT-6 Astra, high.
- If unclear, use GPT-6.1 Sol, medium.
- Do not call Astra separately to route every prompt. Use fixed rules or a cheap Luna router; escalate to Astra only when its extra reasoning is likely to prevent retries or failures.
- Keep prompts, tool calls, context, progress updates, and final responses concise. Avoid unrelated file reads, repeated context, and broad analysis unless requested.
