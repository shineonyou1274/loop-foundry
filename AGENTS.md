# Loop Foundry repository instructions

When a user asks to run this repository, create a personal workflow, apply loop engineering, or extract an automation rule, read `skills/loop-foundry/SKILL.md` completely and follow it.

Do not ask the user for an OpenAI API key. Do not require them to start a Python or web server. Use the current agent's conversation, file, and tool capabilities.

Keep generated `SKILL.md` files portable across Agent Skills hosts. Put host-specific metadata outside the core skill and avoid requiring Codex-only, Claude-only, or Antigravity-only commands in the workflow itself.
