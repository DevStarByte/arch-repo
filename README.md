# DevStarByte Package Repo

One pacman repository for all DevStarByte projects. Add it once and every
package below installs and updates with plain `pacman`.

## Install on Arch Linux

Trust the repo's signing key once:

```sh
curl -sL https://raw.githubusercontent.com/DevStarByte/arch-repo/main/devstarbyte.asc | sudo pacman-key --add -
sudo pacman-key --lsign-key 2F59DCCF36A08B6C4BB5556DF8E225736ED216F0
```

Add the repo to `/etc/pacman.conf`:

```ini
[devstarbyte]
SigLevel = Required
Server = https://raw.githubusercontent.com/DevStarByte/arch-repo/main/$arch
```

Then install whatever you need, e.g.:

```sh
sudo pacman -Sy byteforge
```

Updates arrive with `sudo pacman -Syu`.

## Packages

| Package | Version | Description |
|---------|---------|-------------|
| [byteforge](pkgs/byteforge) | 0.3.1 | A modular, themeable IDE for C# built with Rust and GPUI. Also run `dotnet tool install -g csharp-ls`. |

## Layout

```
pkgs/<name>/PKGBUILD                          package sources (edit these)
x86_64/<name>-<ver>-<rel>-x86_64.pkg.tar.zst  built packages  (generated)
x86_64/devstarbyte.db, devstarbyte.files      repo database   (generated)
x86_64/*.sig                                  signatures      (generated)
devstarbyte.asc                               public signing key
```

## Adding or changing a package

1. Add or edit `pkgs/<name>/PKGBUILD`. The PKGBUILD should build a tagged
   release from the project's own repo (`source=("git+https://github.com/DevStarByte/<project>.git#tag=v$pkgver")`).
   For a new release, tag the project and set `pkgver` to match (`pkgrel=1`).
   When only the packaging changes, bump `pkgrel`.
2. Commit and push. The **Build packages** workflow builds every package
   whose version isn't in `x86_64/` yet, in an Arch container, adds it with
   `repo-add` and commits it back. Computers pick it up on their next
   `pacman -Syu`.

To build by hand instead: `cd pkgs/<name> && makepkg -si`.

## Signing

Packages and the database are signed with the key
`DevStarByte Packages <devstarbyte@protonmail.com>`
(`2F59DCCF36A08B6C4BB5556DF8E225736ED216F0`, public part in
[devstarbyte.asc](devstarbyte.asc)). The workflow signs with the armored
private key in the `GPG_PRIVATE_KEY` secret; without it, builds come out
unsigned and pacman rejects them.
