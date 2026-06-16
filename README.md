# ZSH

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)

Install Zsh and configure a high-performance, dependency-free setup using
[Powerlevel10k](https://github.com/romkatv/powerlevel10k) and a bundled
[Nerd Font](https://github.com/ryanoasis/nerd-fonts). No Oh My Zsh required.

## Features

- **Blazing Fast:** Uses Powerlevel10k with instant-prompt for near-zero startup lag.
- **Steam Deck Ready:** Installs via home-dir extraction to bypass SteamOS read-only protection.
- **Clean Home:** All configuration lives in `~/.config/zsh/` (via `ZDOTDIR`).
- **Powerline Support:** Fully themed prompt with Git status, background jobs, and more.
- **User Extensible:** Simple hook for local, untracked secrets and overrides.

## Config Structure

```
~/.config/zsh/
├── .zshrc                # Main entry point (sources all other files)
├── .zshenv               # Environment vars (ZDOTDIR, module_path, PATH)
├── .p10k.zsh             # Powerlevel10k theme configuration
├── alias.zsh             # Custom aliases
├── ansible.zsh           # Ansible-specific tweaks (force color, etc.)
├── export.zsh            # Environment exports (EDITOR, BROWSER, etc.)
├── functions.zsh         # Custom shell functions
├── keybindings.zsh       # Vim-style navigation and search bindings
├── python.zsh            # Python-specific setup (colors, etc.)
└── local.zsh             # [Optional] Untracked machine-specific overrides
```

## OS Support

| OS Family | Distribution | Method |
|-----------|--------------|--------|
| Archlinux | Arch Linux | `pacman` |
| Archlinux | SteamOS (Steam Deck) | Arch package extraction to `~/.local` |
| Debian | Ubuntu (Focal, Jammy, Noble) | `apt` |
| Debian | Debian | `apt` |
| Darwin | macOS | Homebrew |

## Steam Deck Notes

On SteamOS, the role installs `zsh` by extracting the official Arch Linux
package directly into `~/.local`. This avoids the need to disable the
read-only root partition (`steamos-readonly disable`).

The role dynamically determines the latest `zsh` version using the Arch Linux
Package API and downloads it from the Arch Linux Archive to ensure stability
and compatibility.

## Role Variables

    install: true
    zsh:
      terminal: alacritty
    user: "{{ ansible_facts['user_id'] }}"
    editor: vim
    browser: chromium
    lang: en_US.UTF-8
    path:
      - ~/bin
    python_force_color: 1
    ansible_force_color: 1

## Dependencies

## Secrets and local overrides

This role never templates secrets. To set machine-specific environment
variables (API keys, tokens, etc.), create `~/.config/zsh/local.zsh` by hand
on that machine — it's sourced automatically from `zshrc` if present, and is
git-ignored so it's never accidentally committed.

## Example Playbook

    - hosts: local
      roles:
         - { role: jahrik.zsh }

## Setting the default shell

This role installs and configures Zsh but does **not** change your default login shell automatically (to avoid requiring `sudo` or making system-wide changes without consent).

To make Zsh your default shell:

### Linux / macOS
Run the following command and enter your password when prompted:
```bash
# On Linux (with Zsh in /usr/bin/zsh)
sudo usermod -s /usr/bin/zsh $USER

# On macOS (with Homebrew Zsh)
sudo chsh -s $(which zsh) $USER
```

### Steam Deck (SteamOS)
While you can use `usermod`, the safest way on SteamOS (which survives system updates) is to let `bash` automatically switch to `zsh`. This role adds the following block to your `~/.bashrc` automatically for you:

```bash
if [ -f ~/.local/bin/zsh ]; then
  export SHELL=~/.local/bin/zsh
  exec ~/.local/bin/zsh -l
fi
```

## Testing

This role uses [uv](https://docs.astral.sh/uv/) to manage its testing environment and [Molecule](https://molecule.readthedocs.io/) for verification.

```bash
# Install dependencies
uv sync

# Run all tests
uv run molecule test --all

# Run specific scenario
uv run molecule test -s steamdeck
```

## License

GPLv2

## Author Information

jahrik@gmail.com

https://homelab.business/
