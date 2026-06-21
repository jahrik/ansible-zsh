# ansible-zsh

Ansible role that installs Zsh with Powerlevel10k and Nerd Fonts. Deploys a
minimal configuration to `~/.config/zsh/`. Supports Arch Linux, Debian/Ubuntu,
macOS, and SteamOS (Steam Deck).

## Key Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `install` | `true` | Set to `false` to uninstall and remove all configuration. |
| `editor` | `nvim` | Value of EDITOR env var. |
| `lang` | `en_US.UTF-8` | Value of LANG env var. |
| `python_force_color` | `1` | PY_COLORS env var (conditional). |
| `ansible_force_color` | `1` | ANSIBLE_FORCE_COLOR env var (conditional). |

## Task Flow

`tasks/main.yml` -> `install.yml` or `uninstall.yml` based on `install | bool`

**install.yml:**
1. Detect SteamOS via `/etc/steamos-release`.
2. Include OS-specific tasks: `steamdeck.yml`, `archlinux.yml`, `debian.yml`, or `darwin.yml`.
3. Clone Powerlevel10k to `~/.local/share/zsh/powerlevel10k`.
4. Template config files to `~/.config/zsh/`.
5. Symlink `.zshrc` and `.zshenv` to `$HOME`.

Nerd Fonts are installed by the `jahrik.nerd_fonts` role dependency (see `meta/main.yml`).

**archlinux.yml:** pacman installs zsh, git, fontconfig, unzip.

**debian.yml:** apt installs zsh, git, fontconfig, unzip.

**darwin.yml:** Homebrew installs zsh, git (`become: false` throughout).

**steamdeck.yml:** All tasks run without `become` (root is read-only).
- Resolves latest zsh version via Arch Linux Package API.
- Extracts package from Arch Archive to `~/.local/`.
- Adds automatic zsh-switch block to `~/.bashrc`.

**uninstall.yml:** Removes Zsh artifacts, configuration, and restores `.bashrc`.

## Config Structure

```
~/.config/zsh/
├── .zshrc          # History, completions, vi-mode, sources modules, loads p10k
├── .zshenv         # Sets ZDOTDIR and SteamOS module_path
├── .p10k.zsh      # Powerlevel10k (Python/AWS focused)
├── export.zsh     # EDITOR, LANG, COLORTERM, color flags
├── functions.zsh  # Shell helpers
└── local.zsh      # Untracked machine-specific overrides
```

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
molecule test -s steamdeck
molecule test -s localhost
```

## CI

- **Lint**: yamllint + ansible-lint
- **Molecule**: Ubuntu 24.04 + Arch Linux via Docker (`molecule/default`)
- **Steam Deck**: Arch container with SteamOS simulated (`molecule/steamdeck`)
- **macOS**: `ansible-playbook` against the GHA `macos-latest` runner (`molecule/localhost`)
- **Release**: publishes to Ansible Galaxy on merge to `main`
