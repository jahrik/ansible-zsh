# ansible-zsh

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)

Installs [Zsh](https://www.zsh.org/) and deploys a modular configuration to `~/.config/zsh/`. Sets up [Powerlevel10k](https://github.com/romkatv/powerlevel10k) for the prompt, configures shell environment variables, aliases, and keybindings, and installs [DejaVu Sans Mono Nerd Font](https://github.com/ryanoasis/nerd-fonts) for icon support. Does not rely on Oh My Zsh.

## OS Support

| Platform | Install method |
|---|---|
| Arch Linux | `pacman` |
| Ubuntu / Debian | `apt` |
| macOS | Homebrew (`become: false` throughout) |
| Steam Deck / SteamOS | Extracted binaries to `~/.local/` |

Steam Deck is detected automatically via `/etc/steamos-release`. Because SteamOS has a read-only root filesystem, all tools are installed to the user's home directory without `sudo`. The latest Zsh version is resolved dynamically via the Arch Linux Package API and extracted directly from the archive.

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

## Role Variables

```yaml
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
```

## Secrets and Local Overrides

This role does not template secrets. To set machine-specific environment variables (API keys, tokens, etc.), create `~/.config/zsh/local.zsh` manually. It is automatically sourced from `.zshrc` if present, and is ignored by git.

## Setting the Default Shell

This role installs and configures Zsh but does not change the system's default login shell automatically.

### Linux / macOS
Run the following command and enter your password when prompted:
```bash
# On Linux (with Zsh in /usr/bin/zsh)
sudo usermod -s /usr/bin/zsh $USER

# On macOS (with Homebrew Zsh)
sudo chsh -s $(which zsh) $USER
```

### Steam Deck (SteamOS)
The safest approach on SteamOS is to let `bash` automatically switch to `zsh`, as this survives system updates without requiring changes to `/etc/passwd`. The role automatically adds the following block to your `~/.bashrc`:

```bash
if [ -f ~/.local/bin/zsh ]; then
  export SHELL=~/.local/bin/zsh
  exec ~/.local/bin/zsh -l
fi
```

## Testing

Testing is managed with [uv](https://docs.astral.sh/uv/) and [Molecule](https://molecule.readthedocs.io/).

```bash
uv sync
uv run molecule test               # Default scenario (Ubuntu, Arch)
uv run molecule test -s steamdeck  # SteamOS simulation
uv run molecule test -s localhost  # Local machine (macOS CI runner)
```

## Example Playbook

```yaml
- hosts: local
  roles:
    - { role: jahrik.zsh }
```

## License

GPLv2
