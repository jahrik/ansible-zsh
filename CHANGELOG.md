# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- **Powerlevel10k Integration:** Switched to the high-performance Powerlevel10k theme with instant-prompt.
- **SteamOS Support:** Added home-dir extraction method for Steam Deck to bypass read-only root.
- **macOS Support:** Added Homebrew-based installation and verification.
- **Dynamic Versioning:** Role now automatically resolves the latest Zsh version via the Arch Linux API for SteamOS.
- **Auto Shell-Switch:** Added automatic Zsh switching via `.bashrc` for Steam Deck.
- **uv Environment:** Integrated `uv` for local testing and Molecule management.
- **AGENTS.md:** Added comprehensive AI-agent documentation.

### Changed
- **Modular Tasks:** Split `tasks/main.yml` into OS-specific files (`archlinux.yml`, `debian.yml`, `darwin.yml`, `steamos.yml`).
- **Clean Configuration:** Moved all Zsh configuration to `~/.config/zsh/` via `ZDOTDIR`.
- **Refactored Prompt:** Replaced custom shell prompt script with official theme integration.
- **Updated CI/CD:** Modernized GitHub Actions workflow with parallel jobs for all supported platforms.

### Removed
- **oh-my-zsh:** Removed dependency on heavy framework in favor of native modularity.
- **Secrets in Templates:** Removed old AWS and Chef templates that encouraged baking secrets into tracked files.
- **Automatic Shell Change:** Removed `ansible.builtin.user` tasks that required `sudo` and were often problematic on restricted systems.
