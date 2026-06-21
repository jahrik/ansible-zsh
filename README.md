# ansible-zsh

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-jahrik.zsh-blue?logo=ansible)](https://galaxy.ansible.com/ui/standalone/roles/jahrik/zsh/)

Installs [Zsh](https://www.zsh.org/) with [Powerlevel10k](https://github.com/romkatv/powerlevel10k) and [DejaVu Sans Mono Nerd Font](https://github.com/ryanoasis/nerd-fonts). Deploys a minimal, modular configuration to `~/.config/zsh/`. Does not use Oh My Zsh.

## OS Support

| Platform | Install method |
|---|---|
| Arch Linux | `pacman` |
| Ubuntu / Debian | `apt` |
| macOS | Homebrew (`become: false` throughout) |
| Steam Deck / SteamOS | Extracted binaries to `~/.local/` |

Steam Deck is detected automatically via `/etc/steamos-release`. Because SteamOS has a read-only root filesystem, all tools are installed to the user's home directory without `sudo`.

## Config Structure

```
~/.config/zsh/
├── .zshrc          # Entry point: history, completions, vi-mode, sources modules
├── .zshenv         # Sets ZDOTDIR, SteamOS module_path
├── .p10k.zsh      # Powerlevel10k prompt config
├── export.zsh     # EDITOR, LANG, COLORTERM, color flags
├── functions.zsh  # Shell helpers (man, docker, ssh, aws, etc.)
└── local.zsh      # [Optional] Machine-specific overrides (not managed)
```

## Role Variables

```yaml
install: true         # Set to false to uninstall
editor: nvim          # EDITOR env var
lang: en_US.UTF-8    # LANG env var
python_force_color: 1 # PY_COLORS env var
ansible_force_color: 1 # ANSIBLE_FORCE_COLOR env var
```

## Powerlevel10k

Deploys a curated `.p10k.zsh` focused on Python and Data Engineering workflows. No interactive wizard needed.

**Left prompt:** directory, git status
**Right prompt:** exit status, command duration, background jobs, virtualenv, pyenv, aws, context (SSH only)

The aws segment only appears when typing `aws`, `cdk`, or `sam`. It turns red when the profile name contains `prod`.

### Customizing

Run `p10k configure` to regenerate interactively, or put overrides in `local.zsh`:

```bash
typeset -g POWERLEVEL9K_COMMAND_EXECUTION_TIME_THRESHOLD=5
```

Note: re-running the Ansible role overwrites `.p10k.zsh`.

## Shell Functions

| Function | Description |
|---|---|
| `man` | Colored man pages |
| `docker_inspect_env` | Show container environment variables |
| `docker_inspect_ip` | Show container IP address |
| `docker_shell` | Exec into a running container |
| `ssh_quick` | Generate an ed25519 SSH key |
| `aws_whoami` | Show current AWS caller identity |
| `port` | Show what's listening on a port |
| `jq_less` | Pipe JSON through jq with color paging |
| `loadenv` | Export variables from a .env file |

## Local Overrides

Machine-specific config (Homebrew, PATH, aliases, secrets) goes in `~/.config/zsh/local.zsh`. This file is sourced last and is not managed by Ansible.

## Setting the Default Shell

This role does not change the login shell automatically.

```bash
# Linux
sudo usermod -s /usr/bin/zsh $USER

# macOS (Homebrew Zsh)
sudo chsh -s $(which zsh) $USER
```

On Steam Deck, the role adds an auto-switch block to `~/.bashrc` instead.

## Testing

```bash
yamllint .
ansible-lint
molecule test                 # Default scenario (Ubuntu, Arch containers)
molecule test -s steamdeck    # SteamOS simulation
molecule test -s localhost    # Local machine
```

Step by step:

```bash
molecule converge
molecule verify
molecule destroy
```

## Example Playbook

```yaml
- hosts: local
  roles:
    - { role: jahrik.zsh }
```

## License

GPLv2

## Author Information

jahrik@gmail.com
