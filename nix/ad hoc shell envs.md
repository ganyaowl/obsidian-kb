
### Create shell env

-p (--packages) option

```bash
nix-shell -p <package>
```

type exit or press CTRL-D to exit the shell

### Running programs once

```bash
nix-shell -p <package> --run <arg>
```

### Search for packages

visit [search.nixos.org](https://search.nixos.org/packages)

### Towards reproducibility

```bash
nix-shell -p git --run "git --version" --pure -I nixpkgs=https://github.com/NixOS/nixpkgs/tarball/<hash>
```

1. --run - executes the given bash command within the env created by nix, and exits when it's done
2. --pure - discards most env vars set on your system when running shell
3. -I - determines what to use as source of package declarations

### Refs

1.  [nix manual: nix-shell](https://nix.dev/manual/nix/stable/command-ref/nix-shell)
2.  [nix manual: -I option](https://nix.dev/manual/nix/stable/command-ref/opt-common.html#opt-I)
