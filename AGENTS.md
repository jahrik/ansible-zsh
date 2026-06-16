# AGENTS.md

This file provides guidance to AI coding agents when working with code in this repository.

## Role Overview

Installs [Zsh](https://www.zsh.org/) and deploys a minimal, dependency-free shell environment to `~/.config/zsh/`: aliases, exports, key bindings, functions, and a native powerline-style prompt (directory + git branch segments, no theme framework). Installs a Nerd Font (DejaVu Sans Mono) for the prompt glyphs. Symlinks `~/.zshrc` and `~/.zshenv` to the config directory. Sets zsh as the default user shell.

## Key Variables

| Variable | Default | Description |
|---|---|---|
| `zsh.terminal` | `alacritty` | Value for `$TERM`/`$TERMINAL`/`$COLORTERM` |
| `user` | connecting user (`ansible_facts['user_id']`) | User to set shell for |
| `editor` | `vim` | Default `$EDITOR` |
| `browser` | `chromium` | Default `$BROWSER` |
| `lang` | `en_US.UTF-8` | Default `$LANG` |
| `path` | `[~/bin]` | Extra entries for `$PATH` |

## Task Flow

- `tasks/main.yml` — updates package cache, installs git + zsh, creates `~/.config/zsh`, installs fontconfig/unzip and a Nerd Font into `~/.local/share/fonts` (triggers the `Fc-cache` handler), templates all config files, symlinks `~/.zshrc`/`~/.zshenv`, sets default shell
- `templates/prompt.zsh.j2` — builds `PROMPT` from `vcs_info` directly; no oh-my-zsh or other prompt framework

## Testing Commands

```bash
# Lint
yamllint .

# Full molecule test (Arch container)
molecule test

# Iterative
molecule converge
molecule destroy
```
