The Nix uses lazy evaluation!

## Run .nix 
Interactive evaluation

```bash
nix repl
```


Evaluating Nix files

```bash
nix-instantiate --eval file.nix
```

if no file name is specified, `nix-instantiate --eval` will try to read from default.nix

```bash
nix-instantiate --eval # default.nix
```

for fully evaluated data structure, add the `--strict` option to `nix-instantiate`

```bash
nix-instantiate --eval --strict file.nix
```


## 