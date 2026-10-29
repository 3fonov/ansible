ansible-vault encrypt_string 'my_secret_password' --name 'db_password'

## Linux Mint

Run as the regular desktop user, not with `sudo ansible-playbook`:

```sh
sudo apt update
sudo apt install ansible git
ansible-galaxy collection install -r requirements.yml
ansible-playbook -i localhost, mint.yml --ask-become-pass --ask-vault-pass
```

Run from this repository. The default SSH source files are `.ssh/id_rsa`
and `.ssh/id_rsa.pub`; provide them before running. Omit `--ask-vault-pass`
if the source key is not encrypted with Ansible Vault. Override paths with
`-e source_key=/path/to/id_rsa -e dest_key=/home/you/.ssh/id_rsa`.
Mint installs `gh` and uses GitHub CLI authentication over HTTPS for the private
dotfiles repository and recursive submodules. Before the first run, authenticate:

```sh
sudo apt install gh
gh auth login --hostname github.com --git-protocol https --web
```

Use an account with access to both `3fonov/.dotfiles` and
`3fonov/.private_dotfiles`. The playbook configures GitHub CLI as Git's credential
helper. It rewrites SSH submodule URLs to HTTPS for the dotfiles commands only.
Existing checkouts are updated with Git using the same credentials.
The macOS entry point retains SSH cloning. The dotfiles branch
must contain the `mint` script (present in the local dotfiles checkout).

`mint.yml` preserves the SSH → Git → Zsh → tools → Neovim → dotfiles order,
the Git identity, and existing tags such as `install`, `ssh`, `git-personal`,
`zsh`, `neovim`, `dotfiles` and `stow`. It uses sudo only for system packages
and the login shell; user files belong to the invoking user. The platform
guard and APT prerequisites run even when selecting tags.

```sh
ansible-playbook -i localhost, mint.yml --syntax-check
ansible-playbook -i localhost, mint.yml --list-tags
ansible-playbook -i localhost, mint.yml --ask-become-pass --tags neovim
```

### Compatibility with local.yml

`local.yml` remains the macOS entry point. Shared dotfiles tasks default to
Homebrew and `./mac`; Mint selects APT and `./mint` instead. Stow applies
the folders selected by that script, so review existing configuration files
for conflicts before the first run. The upstream Stow script runs on every
normal invocation; no configuration files are forcibly removed by this playbook.

| Homebrew tool | Mint installation |
| --- | --- |
| fd | `fd-find`, with `~/.local/bin/fd` symlink |
| poetry | `python3-poetry` when available |
| fzf, jq, ripgrep, tmux, direnv, rclone, stow, zsh, neovim | APT packages |
| tldr, cookiecutter, gh, zoxide, lazygit, yazi, ghostty | APT when a candidate exists; otherwise reported and skipped |
| goto, font-hack-nerd-font, macpacker, mole, lab421/tap/forel | Omitted; no equivalent configured |

The optional list is configurable using `mint_optional_packages`. The playbook
does not add third-party repositories or download release binaries. APT versions
depend on your Mint release; in particular, Neovim may be older than your plugins
require. Nerd Fonts and any additional dependencies of your private dotfiles
need separate installation. Ensure `~/.local/bin` is on your PATH for `fd`.

Syntax validation on macOS does not verify package installation, desktop
behavior or dotfiles application on a real Mint machine. Check mode is a
preview, not proof of a clean first installation with cloned repositories.
