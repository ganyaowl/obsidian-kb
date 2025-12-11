### Enabling Flakes

1. Add `nix.settings.experimental-features = [ "nix-command" "flakes" ];` in your conf.nix
2. Home-manager: ```
   ```nix
	nix = {
		package = pkgs.nix;
		settings.experimental-features = [ "nix-command" "flakes" ];
	};
	 ```
3. nix.conf : `experimental-features = nix-command flake`

### Flakes Initialization

```bash
nix flake init
```

### Nix commands

1. `nix run` -> runs  a packaged binary. `outputs.packages."SYSTEM".default`
2. `nix build` -> builds a package. `outputs.packages."SYSTEM".default`
3. `nix develop` -> activates a dev shell. `outputs.devShells."SYSTEM".default`
4. `nixos-rebuild` -> builds a nixos system. `outputs.nixosConfiguration."HOSTNAME"`
5. `home-manager` -> builds a home configuration. `outputs.homeConfiguration."USERNAME"`

### Ref

1. [Ultimate Nix Flakes Guide by vimjoyer](https://youtu.be/JCeYq72Sko0)