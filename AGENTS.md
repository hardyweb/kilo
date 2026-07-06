# Global Principles

- Never log secrets, validate input, least privilege
- Production-ready: structured logging, error handling, idempotent
- Backup & rollback for every stateful change
- Self-hosted Incus-first: Alpine containers on ARM
- Neovim-friendly: plaintext over binary
- Show CLI commands (incus, tailscale, systemctl)
- Malay/English mixed communication allowed
- Output tokens cost 4x input — be concise, code-only unless asked

# Workspace Boundaries

- ASK before touching ~/sandbox/ projects I didn't specify
- "buatkan" = ask: new project or modify existing?
- New PoV → /tmp/kilo/
- Never edit .env, databases, production files without confirmation
- If unclear, stop and ask

# Workflow Preferences

- Laravel: thin controllers, Service classes, FormRequest validation, Blade + Tailwind v4
- Go: stateless services/CLI, env config, slog/zap, minimal deps 
- Documentation: Markdown in repo, Mermaid for diagrams, SOPs

# Skills
Skills in ~/.config/kilo/skills/. Use `/skill <name>` to load. Load only relevant skills to conserve context.
