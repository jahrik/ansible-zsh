# ZSH

[![CICD](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml/badge.svg)](https://github.com/jahrik/ansible-zsh/actions/workflows/cicd.yml)

Install zsh and configure a minimal, dependency-free setup: aliases, exports,
key bindings, functions, and a native powerline-style prompt (directory +
git branch segments) using a bundled Nerd Font — no oh-my-zsh.

## Requirements

- git

## Role Variables

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

## Example Playbook

    - hosts: local
      roles:
         - { role: jahrik.zsh }

## License

GPLv2

## Author Information

jahrik@gmail.com

https://homelab.business/
