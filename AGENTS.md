# ansible-zsh

Installs Zsh and deploys a clean, modular configuration using Powerlevel10k
and Nerd Fonts. Supports Arch Linux, Debian/Ubuntu, macOS, and SteamOS
(Steam Deck).

## Key Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `install` | `true` | Set to `false` to uninstall and remove all configuration. |
| `zsh.terminal` | `alacritty` | Terminal emulator to optimize for. |
| `user` | `ansible_facts['user_id']` | Target user for configuration. |

## Task Flow

`tasks/main.yml` -> `install.yml` or `uninstall.yml` based on `install | bool`

**install.yml:**
1. Detect SteamOS via `/etc/steamos-release`.
2. Include OS-specific tasks: `steamdeck.yml`, `archlinux.yml`, `debian.yml`, or `darwin.yml`.
3. Create `~/.local/share/fonts`, download DejaVu Nerd Fonts, notify `Fc-cache`.
4. Clone Powerlevel10k to `~/.local/share/zsh/powerlevel10k`.
5. Template modular config files to `~/.config/zsh/`.
6. Symlink `.zshrc` and `.zshenv` to `$HOME`.

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
├── .zshrc                # Sources modular files and loads p10k
├── .zshenv               # Sets ZDOTDIR and module_path
├── .p10k.zsh             # Powerlevel10k classic style
├── alias.zsh             # System aliases
├── ansible.zsh           # Ansible colors and env
├── export.zsh            # PATH and default apps
├── functions.zsh         # Shell helpers
├── keybindings.zsh       # Vim-style bindings
└── local.zsh             # Untracked user overrides
```

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
