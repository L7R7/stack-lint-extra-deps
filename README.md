# Lint Stackage (extra) Deps

Maintaining `extra-deps` in a `stack.yaml` (or `packages` in a `snapshot.yaml`)
is a pain. If an extra dep has been moved into your resolver, you should remove
it. If your extra dep has a new version on Hackage, you may want to update it.
If you have a `git` dependency, you may want to know if that change is now
available in a versioned release on Hackage.

All of these are manual, annoying, and error prone to check by hand.

![](./files/example.png)

## Install

Get the Download URL for the latest release,

```sh
url=$(curl --silent https://api.github.com/repos/freckle/lsd/releases/latest |
  jq '.assets[].browser_download_url | select(.|test("x86_64-linux.tar.gz$"))' --raw-output)
```

(You can also just browse [Releases][].)

[releases]: https://github.com/freckle/lsd/releases

```console
% curl -L "$url" | tar xzf - && mv lsd/lsd ~/.local/bin && rmdir lsd
% which lsd
~/.local/bin/lsd
```

## Usage

```console
% lsd --help
Usage: lsd [-r|--resolver ARG] [--exclude PATTERN] [--checks ARG]
           [-f|--format ARG] [--no-exit] [-c|--color ARG] [-v|--verbose] [PATH]
           [PATTERN]
  Lint Stackage (extra) Deps

Available options:
  -r,--resolver ARG        Override resolver from stack.yaml
  --exclude PATTERN        Exclude deps by glob
  --checks ARG             Checks to run, one of all, git, hackage
  -f,--format ARG          Output format, one of detailed
  --no-exit                Exit successfully even if suggestions found
  -c,--color ARG           When to use color: auto, always, never
  -v,--verbose             Log verbosely
  PATH                     Path to config to lint (default: "stack.yaml")
  PATTERN                  Limit deps matching glob
  -h,--help                Show this help text
```

## Workflow

This is intended for use as part of a "Resolver Bump", when moving a project
from one Stackage resolver to a (presumably) newer one. After doing so, run this
linter. It will suggest any changes to your `extra-deps` that apply under the
new resolver (such as redundant Hackage deps).

## Features

- [x] Suggest update when there is a newer version of a Hackage dep available
- [x] Suggest removal when a Hackage dep is in the resolver at a same-or-newer
      version
- [ ] Confirm all Hackage `extra-deps` use sha-pinning
- [x] Suggest update when there are newer commits in a git dep
- [x] Suggest replacement when there is a Hackage version of a git dep at a
      same-or-newer version
- [ ] Apply custom rules

---

[LICENSE](./LICENSE) | [CHANGELOG](./CHANGELOG.md)
