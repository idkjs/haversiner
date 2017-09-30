# Notes on creating an opam project build
- src: <https://medium.com/@bobbypriambodo/lightweight-ocaml-docker-images-with-multi-stage-builds-f7a060c7fce4>

## clone test project and cd into directory
```bsh
git clone https://github.com/bobbypriambodo/haversiner.git
cd haversiner
```

## install opam compiler

- run `opam switch 4.04.2-haversiner --alias-of 4.04.2`
- opam switch creates a self-contained OCaml compiler related to this project.
- by default, opam installs all deps globally and uses the one's it needs in a particulary project.
- opamv2 has a solution called local switches which will make this easier.
- the above command builds a compiler for OCaml 4.04.2 and aliases it to 4.04.2-haversiner. So we have a specific compiler for this project that we can identify by name. 
- TIP: use a single switch for a single project (utilizing the --alias-of flag), that will keep your packages self-contained for each project.

- command output: 
```bsh

=-=- Installing compiler 4.04.2 -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫
[default.comp] https://github.com/ocaml/ocaml/archive/4.04.2.tar.gz downloaded
Now compiling OCaml. This may take a while, please bear with us...
Done.

=-=- Gathering sources =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫

=-=- Processing actions -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫
∗  installed base-bigarray.base
∗  installed base-threads.base
∗  installed base-unix.base
Done.
# To setup the new switch in the current shell, you need to run:
eval `opam config env`

```

## Opam Pin
- opam pin will `pin` a particular package to a particular source
- run: `opam pin add -yn haversiner .`
- the `-yn` flags are yes and no answers to question asked by the opam pin command.
- output:

```bsh
Package haversiner does not exist, create as a NEW package ? [Y/n] y
haversiner is now path-pinned to /Users/shingdev/repos/github.com/haversiner

[haversiner] /Users/shingdev/repos/github.com/haversiner/ synchronized
[haversiner] Installing new package description from
/Users/shingdev/repos/github.com/haversiner
```

## Install Depext Dependency Tool
- see: <https://github.com/ocaml/opam-depext>
- install depext in the haversiner project which will allow us to install dependencies. Its a dep manager.
- the depext command is used for installing OS-specific system packages that our libraries might need
- run: `opam depext haversiner`
- output:
```bsh
OPAM plugin depext is not installed. Install it on the current switch? [Y/n] y
The following actions will be performed:
  ∗  install depext 1.0.5
```
- note how it going to install the depext plugin on our current switch, which is the compiler aliased as `4.04.2-haversiner`

- total output:
```bsh
OPAM plugin depext is not installed. Install it on the current switch? [Y/n] y
The following actions will be performed:
  ∗  install depext 1.0.5

=-=- Gathering sources =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫
[depext] Archive in cache

=-=- Processing actions -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫
∗  installed depext.1.0.5
Done.

=-=- Carrying on to "opam depext haversiner" =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=  🐫

# Detecting depexts using flags: x86_64 osx homebrew
# The following system packages are needed:
#  - openssl
#  - pcre
#  - pkg-config
# All required OS packages found.

```

## Install Project Dependencies
- reads the `harversiner.opam` dependency file(node's package.json) and installs the listed dependencies.
- run: `opam install --deps-only haversiner`