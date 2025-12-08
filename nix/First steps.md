## Ad hoc shell envs
### Create shell env

-p (--packages) option

```bash
nix-shell -p <package>
```

type `exit` or press `CTRL-D` to exit the shell

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

1. --run : executes the given bash command within the env created by nix, and exits when it's done
2. --pure : discards most env vars set on your system when running shell
3. -I : determines what to use as source of package declarations

### Refs

1.  [nix manual: nix-shell](https://nix.dev/manual/nix/stable/command-ref/nix-shell)
2.  [nix manual: -I option](https://nix.dev/manual/nix/stable/command-ref/opt-common.html#opt-I)

## Reproducible interpreted scripts

shebang line
```bash
#!/usr/bin/env nix-shell
```

Script example:

```bash
# nixpkgs-releases.sh
#!/usr/bin/env nix-shell
#! nix-shell -i bash --pure
#! nix-shell -p bash cacert curl jq python3Packages.xmljson
#! nix-shell -I nixpkgs=https://github.com/NixOS/nixpkgs/archive/2a601aafdc5605a5133a2ca506a34a3a73377247.tar.gz

curl https://github.com/NixOS/nixpkgs/releases.atom | xml2json | jq .
```

1. -i : specified interpreter
2. --pure : prevent the script from implicitly using programs that may exist on host system
3. -p : list of packages required for the script to run

Make scripts executable

```bash
chmod +x <script>.sh && ./<script>.sh
```

## Declarative shell envs `shell.nix`

`shell.nix` allows you to:
- auto run bash commands during env activation
- auto set env vars
- put env definition under git (any vcs) and reproduce it on other machines

### A basic shell.nix file

```nix
# shell.nix
let
  nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
  pkgs = import nixpkgs { config = {}; overlays = []; };
in

pkgs.mkShellNoCC {
  packages = with pkgs; [
    cowsay
    lolcat
  ];
}
```

explicitly set `config` and `overlays` to avoid them being overriden by [global configuration](https://nixos.org/manual/nixpkgs/stable/#chap-packageconfig)

mkShellNoCC : a function that takes as an argument an attribute set. `packages` with a list containing two items from the pkgs attribute set.

- mkShellNoCC - is a func that produces a temp env without a compiler toolchain. Also it's a wrapper around `mkDerivation`
- mkShell - is a func that produces a temp env with a compiler toolchain.

To enter the env run `nix-shell` in the same dir as `shell.nix`:

```bash
nix-shell
cowsay hello | lolcat
```
#### *extra* not quite understand
You may encounter examples of `mkShell` or `mkShellNoCC` that add packages to the `buildInputs` or `nativeBuildInputs` attributes instead. `mkShellNoCC` is a [wrapper around `mkDerivation`](https://nixos.org/manual/nixpkgs/stable/#sec-pkgs-mkShell), so it takes the same arguments as `mkDerivation`, such as `buildInputs` or `nativeBuildInputs`. The `packages` attribute argument to `mkShellNoCC` is simply an alias for `nativeBuildInputs`.

### Environment variables

Any attribute name passed to `mkShellNoCC` that is not reserved otherwise and has a value which can be *coerced* to a string will end up as an env var.

```nix
# shell.nix
let
  nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
  pkgs = import nixpkgs { config = {}; overlays = []; };
in

pkgs.mkShellNoCC {
  packages = with pkgs; [
    cowsay
    lolcat
  ];
  
GREETING = "Hello, Nix!";
}
```

### Startup commands

To run some commands before entering the shell env place in the `shellHook` attr provided to mkShellNoCC

```nix
# shell.nix
let
  nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-25.11";
  pkgs = import nixpkgs { config = {}; overlays = []; };
in

pkgs.mkShellNoCC {
  packages = with pkgs; [
    cowsay
    lolcat
  ];
  
GREETING = "Hello, Nix!";

shellHook = ''
    echo $GREETING | cowsay | lolcat 
    '';
}
```

### Refs

1. [`mkShell` doc](https://nixos.org/manual/nixpkgs/stable/#sec-pkgs-mkShell)
2. [`nix-shell` doc](https://nix.dev/manual/nix/stable/command-ref/nix-shell)
3. Nixpkgs [shell functions and utilities](https://nixos.org/manual/nixpkgs/stable/#ssec-stdenv-functions) documentation