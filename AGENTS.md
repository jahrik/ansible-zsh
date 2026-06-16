# Agent Guide: jahrik.zsh

Install zsh and configure a minimal, dependency-free setup: aliases, exports,
key bindings, functions, and a native powerline-style prompt using a bundled
Nerd Font.

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `install` | `true` | Whether to install or uninstall the role. |
| `zsh.terminal` | `alacritty` | Terminal emulator to optimize for. |
| `user` | `ansible_facts['user_id']` | Target user for home-dir configuration. |
| `editor` | `vim` | Default editor. |
| `browser` | `chromium` | Default browser. |
| `lang` | `en_US.UTF-8` | System language. |
| `path` | `['~/bin']` | List of additional PATH entries. |

## Task Flow

- `tasks/main.yml`: Thin dispatcher based on `install` variable.
- `tasks/install.yml`: OS detection and dispatching to OS-specific tasks, followed by shared configuration.
- `tasks/uninstall.yml`: Removal of configuration and packages (best-effort).
- OS-specific tasks: `archlinux.yml`, `debian.yml`, `darwin.yml`, `steamos.yml`.

## Testing

Local linting:
```bash
uv run yamllint .
uv run ansible-lint
```

Molecule testing:
```bash
uv run molecule test               # Default scenario (Ubuntu, Arch)
uv run molecule test -s steamdeck  # SteamOS simulation
uv run molecule test -s localhost  # Local machine (macOS/SteamOS/Linux)
```
