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

1. `nix run` -> runs  a packaged binary.
2. `nix build` -> builds a package.
3.  sdf

### Ref

1. [Ultimate Nix Flakes Guide by vimjoyer](https://youtu.be/JCeYq72Sko0)