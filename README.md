# Model Routing

A personal Codex skill and optional Cursor rule for recommending a model and reasoning effort before starting work. It uses a fixed, token-conscious routing policy and requests your preference through an interactive approval prompt when the host supports it.

## Routing policy

- **GPT-6 Luna · Low:** small, exact edits and routine UI work.
- **GPT-6.1 Sol · Medium:** normal features, bug fixes, coordinated changes, and tests; also the default when unclear.
- **GPT-6 Astra · High:** ambiguous debugging, security, authentication, Redis/concurrency, Octane, architecture, and high-risk production behavior.

These are configured recommendations, not a live model availability or pricing check. Adapt the names to the models your account supports.

## Installation

Clone this repository:

```sh
git clone https://github.com/ardavanshamroshan/model-routing.git
cd model-routing
```

### Codex skill

Copy the skill into your personal skills directory. If you already have a model-routing skill, back it up before replacing it.

```sh
mkdir -p "$HOME/.codex/skills/model-routing"
cp model-routing/SKILL.md "$HOME/.codex/skills/model-routing/SKILL.md"
```

Start a new chat or reload the application if the skill does not appear in the current session.

### Optional Cursor skill and rule

For the personal skill:

```sh
mkdir -p "$HOME/.cursor/skills/model-routing"
cp model-routing/SKILL.md "$HOME/.cursor/skills/model-routing/SKILL.md"
```

The included rule has `alwaysApply: true`. To enable it for a Cursor project, run the following from that project's root, replacing the source path with your clone's location:

```sh
mkdir -p .cursor/rules
cp /path/to/model-routing/rules/model-routing.mdc .cursor/rules/model-routing.mdc
```

Use the skill for explicit invocation, or the rule for automatic routing in the project. Tool support differs between hosts; the interactive prompt may fall back to a chat question.

## Usage

Invoke the skill with a concrete task:

```text
$model-routing Explain the homepage route in routes/web.php. Do not edit files.
```

```text
$model-routing Make the email field required in the account form and update its feature test.
```

```text
$model-routing Which model and reasoning effort should handle this task?
```

The agent recommends a model and effort. If the active settings differ or are unknown, it presents **Approve** and **Keep the current model** choices. It waits for a reply before starting dependent work.

- Choose **Keep the current model** to continue with the existing settings.
- Choose **Approve** to accept the recommendation, then apply it in the app's model selector and confirm the change in chat.

## Limitations

- This skill does **not** switch the active model. Approval records your preference; the model selector performs the actual change.
- Interactive UI requires the host's `functions.request_user_input_async` tool. Without it, the skill asks in chat.
- Keeping an asynchronous prompt pending requires a supported interruptible wait tool. The skill instructs the agent not to finish the turn prematurely, but cannot guarantee the host's UI lifetime.
- Waiting is intentional and can keep a turn open until you answer or stop it. Time passing is never treated as approval.
- Model names, tool names, and skill discovery behavior can vary by host and version.

## Repository contents

- `model-routing/SKILL.md` — the personal skill.
- `rules/model-routing.mdc` — the optional always-on rule.

The skill and rule are distributed as the current saved versions; the instructions above describe their behavior rather than adding an automatic switching capability.
