# Mint workstation setup

Approved in chat: use APT and add mint.yml while leaving local.yml unchanged.
Preserve task order, tags, SSH paths and Git identity; adapt Zsh, Neovim and
Stow for Mint and use the existing dotfiles mint entry point. Document missing
APT equivalents and validate syntax without provisioning this macOS host.

APT was selected over Linux Homebrew. Reuse shared tasks with defaulted
platform variables where practical; isolate Mint package and shell tasks.
