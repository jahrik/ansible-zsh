# AGENTS.md

Ansible role that installs Zsh, Powerlevel10k, and Nerd Fonts with a modular `~/.config/zsh/` setup. Supports Arch Linux, Debian/Ubuntu, macOS, and SteamOS (Steam Deck).

## Task Flow

`tasks/main.yml` delegates to OS-specific playbooks based on `ansible_os_family` and `install` flag.

- **Steam Deck (`steamdeck.yml`)**: Root is read-only. Downloads the Arch Linux binary, extracts to `~/.local/`, and manages aliases.
- **macOS (`darwin.yml`)**: Uses Homebrew (`become: false`).
- **Linux (`archlinux.yml`, `debian.yml`)**: Uses native package managers (`pacman`, `apt`) to install zsh, git, fontconfig, unzip.
- **Uninstall (`uninstall.yml`)**: Removes Zsh files, `.config/zsh` directory, and restores `.bashrc`.

## Role Variables

| Variable | Default | Description |
|---|---|---|
| `install` | `true` | Set to `false` to uninstall. |
| `editor` | `nvim` | Sets `$EDITOR`. |
| `lang` | `en_US.UTF-8` | Sets `$LANG`. |
| `python_force_color` | `1` | Forces Python colored output. |
| `ansible_force_color`| `1` | Forces Ansible colored output. |

## Config Structure

```text
~/.config/zsh/
├── .zshrc          # Main entry point: history, vi-mode, modules
├── .zshenv         # ZDOTDIR setup
├── .p10k.zsh       # Powerlevel10k theme
├── export.zsh      # Environment variables
├── functions.zsh   # Custom shell functions
└── local.zsh       # Untracked machine-specific overrides
```

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```

### Examples

Test specific scenarios locally:

```bash
molecule test -s steamdeck
molecule test -s localhost
```

## CI

- **Lint**: yamllint + ansible-lint
- **Molecule**: Default (Ubuntu/Arch containers), Steamdeck (Arch container), macOS (GHA macOS runner)
- **Release**: Publishes to Ansible Galaxy on merge to `main`
