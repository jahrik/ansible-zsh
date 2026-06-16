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

The version is pinned in `defaults/main.yml` via `zsh_steamos_version`. To
bump it:
1. Find the latest version on [archive.archlinux.org](https://archive.archlinux.org/packages/z/zsh/).
2. Update `zsh_steamos_version` in `defaults/main.yml`.
3. Test the `steamdeck` molecule scenario.

## Role Variables

    install: true
    zsh_steamos_version: "5.9.1-1"
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

## License

GPLv2

## Author Information

jahrik@gmail.com

https://homelab.business/
