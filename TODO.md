# TODO: SteamOS + macOS support for ansible-zsh

Goal: bring this role up to the same level of OS coverage as `ansible-nvim`
(SteamOS via home-dir binary extraction + macOS via Homebrew, both with their
own molecule scenarios and CI jobs), following the existing pattern already
established in `ansible-alacritty`/`ansible-nvim`.

## Plan

- [x] `defaults/main.yml`: add `install: true` and `zsh_steamos_version`
      (pinned archived Arch package version/release for the SteamOS binary
      extraction fallback — verify the URL resolves on
      `archive.archlinux.org/packages/z/zsh/` before pinning)
- [x] `meta/main.yml`: add `Debian`, `Ubuntu` (noble/jammy), `macOS`,
      `GenericLinux` platforms; update `galaxy_tags` (drop `ohmyzsh`, add
      `shell`, `steamdeck`, `steamos`, `powerline`)
- [x] Split `tasks/main.yml` into a thin dispatcher + `tasks/install.yml` /
      `tasks/uninstall.yml`, mirroring `ansible-alacritty`'s `install |
      default(true) | bool` pattern
- [x] `tasks/install.yml`: detect SteamOS via `/etc/steamos-release`, guard
      the existing Arch/Ubuntu cache-update tasks against running on
      SteamOS, dispatch to one of `archlinux.yml` / `debian.yml` /
      `darwin.yml` / `steamos.yml`, then run the OS-agnostic shared steps
      (Nerd Font download, `~/.config/zsh` templating, symlinks, shell
      change using a dynamically-set `zsh_shell_path` fact)
- [x] `tasks/archlinux.yml`: pacman install zsh/git/fontconfig/unzip,
      `set_fact: zsh_shell_path: /usr/bin/zsh`
- [x] `tasks/debian.yml`: apt install zsh/git/fontconfig/unzip, same fact
- [x] `tasks/darwin.yml`: `community.general.homebrew` install zsh + git,
      `become: false` throughout, detect path via `which zsh`
- [x] `tasks/steamos.yml`: extract zsh binary from the archived Arch
      package straight to `~/.local/bin/zsh` (no pacman/root, mirrors
      `ansible-alacritty/tasks/steamos.yml`), non-fatal check that `git`
      is present (SteamOS ships it by default), `set_fact:
      zsh_shell_path: ~/.local/bin/zsh`
      - Note: setting the login shell via the `user` module still needs
        `become: true` even on SteamOS — that's fine, it only touches
        `/etc/passwd`, not the dm-verity-protected `/usr` partition the
        "never disable steamos-readonly" rule is actually about
- [x] `tasks/uninstall.yml`: reverse of the above per OS, plus best-effort
      reset of the shell back to `/bin/bash`
- [x] `molecule/steamdeck/`: `molecule.yml` (privileged Arch container +
      `seccomp=unconfined`), `prepare.yml` (fakes `/etc/steamos-release`,
      installs `curl`/`unzip` via pacman to simulate what's already on real
      hardware), `converge.yml`, `verify.yml` (assert
      `~/.local/bin/zsh`, `~/.config/zsh/zshrc`, `~/.zshrc` symlink, Nerd
      Font file), `requirements.yml`
- [x] `molecule/localhost/`: local-connection scenario for the macOS CI
      runner (mirrors `ansible-nvim`) — **do not run this locally**, it
      mutates the real machine's `~/.zshrc` and default shell; it's only
      meant to be invoked via `ansible-playbook ... -i "localhost," -c
      local` on an ephemeral GitHub Actions macOS runner
- [x] `.github/workflows/cicd.yml`: add `steamdeck` job (`molecule_args: -d
      docker -s steamdeck`) and `macos` job (`runs-on: macos-latest`,
      direct `ansible-playbook` invocation of `molecule/localhost/{converge,verify}.yml`,
      no Docker); update `release.needs` to `[molecule, macos]` (steamdeck
      not required, matching `ansible-nvim`)
- [x] `README.md`: add an "OS Support" table and a "Steam Deck Notes"
      section documenting the binary-extraction approach and how to bump
      the pinned version
- [x] `AGENTS.md`: document the new task flow (install/uninstall split,
      per-OS dispatch, SteamOS detection)
- [x] Lint (`yamllint .`, `ansible-lint`) and test locally with `mtest test`
      (default scenario) and `mtest test -s steamdeck` — the macOS path has
      no local verification, only the GitHub Actions `macos` job on the PR
- [x] Commit, push, open PR, watch CI to green
