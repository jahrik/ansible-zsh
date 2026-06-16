# ZSH

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)

Install zsh and configure a minimal, dependency-free setup: aliases, exports,
key bindings, functions, and a native powerline-style prompt (directory +
git branch segments) using a bundled Nerd Font — no oh-my-zsh.

## Requirements

- git

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
