# ansible-zsh

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-jahrik.zsh-blue?logo=ansible)](https://galaxy.ansible.com/ui/standalone/roles/jahrik/zsh/)

Installs [Zsh](https://www.zsh.org/), [Powerlevel10k](https://github.com/romkatv/powerlevel10k), and [Nerd Fonts](https://github.com/ryanoasis/nerd-fonts). Configures Zsh modularly in `~/.config/zsh/`. Does not use Oh My Zsh.

## OS Support

| Platform | Install Method |
|---|---|
| Arch Linux | `pacman` |
| Ubuntu / Debian | `apt` |
| macOS | Homebrew |
| Steam Deck / SteamOS | Extracted binaries to `~/.local/` |

## Usage

**Install role:**

```bash
ansible-galaxy install jahrik.zsh
```

**Example Playbook:**

```yaml
- hosts: localhost
  roles:
    - { role: jahrik.zsh, install: true, editor: nvim }
```

### Role Variables

| Variable | Default | Description |
|---|---|---|
| `install` | `true` | Set to `false` to uninstall. |
| `editor` | `nvim` | Sets `$EDITOR`. |
| `lang` | `en_US.UTF-8` | Sets `$LANG`. |
| `python_force_color` | `1` | Forces Python colored output. |
| `ansible_force_color`| `1` | Forces Ansible colored output. |

## Configuration

The setup uses `~/.config/zsh/` rather than the home directory for better organization.

```text
~/.config/zsh/
├── .zshrc          # Entry point
├── .zshenv         # Sets ZDOTDIR
├── .p10k.zsh       # Powerlevel10k config
├── export.zsh      # Environment exports
├── functions.zsh   # Helpers (docker, aws, etc.)
└── local.zsh       # Optional, unmanaged machine-specific overrides
```

### Steam Deck Notes

SteamOS has a read-only root. This role installs zsh binaries to `~/.local/bin/zsh` without `sudo`.
Do not change your login shell to a local binary, it may break SteamOS login.
Instead, configure your terminal emulator (e.g. Konsole) to run `~/.local/bin/zsh` automatically.

## Testing

```bash
uv sync
source .venv/bin/activate
yamllint .
ansible-lint
molecule test
```

### Examples

Run specific Molecule scenarios:

```bash
molecule test -s steamdeck
molecule test -s localhost
```

Step-by-step Molecule execution:

```bash
molecule converge
molecule verify
molecule destroy
```

## License

GPLv2

## Author Information

jahrik@gmail.com
