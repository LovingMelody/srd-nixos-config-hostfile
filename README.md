This repository contains the Nix / NixOS configuration for all of my systems. See [nixos-flake](https://github.com/srid/nixos-flake) if you want to create your own configuration from scratch.

## Setup

To use this repository as base configuration for your new machine running:

### NixOS Linux

> [!TIP] 
> For a general tutorial, see https://nixos.asia/en/tutorial/nixos-install

- Install NixOS
  - Hetzner dedicated from Linux Rescue system: https://github.com/numtide/nixos-anywhere (see [blog post](https://galowicz.de/2023/04/05/single-command-server-bootstrap/); example PR: https://github.com/srid/nixos-config/pull/35 where I had to configure networking manually)
    - Copy from existing configuration (eg: ax41.nix)
    - Make networking configuration changes. 
    - Run nixos-anywhere from a Linux system, targetting `root@<ip>`
    - Wait for reboot; `ssh srid@<ip>`; profit!
  - Digital Ocean
    - Legacy/manual approach: [nixos-infect](https://github.com/elitak/nixos-infect)
    - Modern/automate approach: Custom image + colerama; cf. [Zulip](https://nixos.zulipchat.com/#narrow/stream/413948-nixos/topic/Deploying.20to.20DigitalOcean) and [example](https://github.com/fpindia/fpindia-chat)
  - X1 Carbon: https://srid.ca/x1c7-install
  - Windows (via WSL): https://github.com/nix-community/NixOS-WSL
- Clone this repo anywhere
- Edit `flake.nix` to use your system hostname as a key of the `nixosConfigurations` set
- Edit `users/config.nix` to contain your users
- Run `nix run`. That's it. Re-open your terminal.

### macOS

- [Install Nix](https://nixos.asia/en/install)
- Install [nix-darwin](https://github.com/LnL7/nix-darwin) 
    - This will create a `~/.nixpkgs/darwin-configuration.nix`, but we do not need that. 
- Clone this repo anywhere
- Edit `flake.nix` to use your system hostname as a key of the `darwinConfigurations` set
- Edit `users/config.nix` to contain your users
- Run `nix run`.[^cleanup] That's it. Re-open your terminal.

[^cleanup]: You might have to `rm -rf /etc/nix/nix.conf`, so our flake.nix can do its thing.

## Architecture

Start from `flake.nix` (see [Flakes](https://nixos.wiki/wiki/Flakes)). [`flake-parts`](https://flake.parts/) is used as the module system. 

### Directory layout 

- `home`: home-manager config (shared between Linux and macOS)
- `nixos`: nixos modules for Linux
- `nix-darwin`: nix-darwin modules for macOS
- `users`: user information
- `secrets.yaml` (and `.sops.yaml`):  sops-nix secrets
- `systems`: top-level configuration.nix('ish) for various systems

## Tips

- To update NixOS (and other inputs) run `nix flake update`
  - You may also update a subset of inputs, e.g.
      ```sh
      nix flake lock --update-input nixpkgs --update-input darwin --update-input home-manager
      # Or, `nix run .#update`
      ```
- To free up disk space,
    ```sh-session
    sudo nix-env -p /nix/var/nix/profiles/system --delete-generations +2
    sudo nixos-rebuild boot
    ```
- To autoformat the project tree using nixpkgs-fmt, run `nix fmt`.
- To build all flake outputs (locally or in CI), run `nix run nixpkgs#nixci`

## Discussion

If you wish to discuss about this config, [join the Zulip](https://nixos.zulipchat.com/login/?next=/).
