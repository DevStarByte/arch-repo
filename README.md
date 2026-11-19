# DevStarByte Package Repo

One pacman repository for all DevStarByte projects. Add it once and every
package below installs and updates with plain `pacman`.

## Install on Arch Linux

Trust the repo's signing key once:

```sh
curl -sL https://raw.githubusercontent.com/DevStarByte/arch-repo/master/devstarbyte.asc | sudo pacman-key --add -
sudo pacman-key --lsign-key 2F59DCCF36A08B6C4BB5556DF8E225736ED216F0
```

Add the repo to `/etc/pacman.conf`:

```ini
[devstarbyte]
SigLevel = Required
Server = https://raw.githubusercontent.com/DevStarByte/arch-repo/master/$arch
```

Then install whatever you need, e.g.:

```sh
sudo pacman -Sy byteforge
```

Updates arrive with `sudo pacman -Syu`.

## Packages

| Package | Description |
|---------|-------------|
| [byteforge](pkgs/byteforge) | The latest stable ByteForge release: a modular, themeable IDE for C# built with Rust and GPUI. Also run `dotnet tool install -g csharp-ls`. |
| [byteforge-beta](pkgs/byteforge-beta) | The newest ByteForge build, pre-release or stable, whichever is newer. Replaces `byteforge`. |

`pacman -Si <package>` shows the version the repo carries.

### ByteForge

`byteforge` and `byteforge-beta` are updated by ByteForge's own release workflow: every version tag
pushed to [DevStarByte/ByteForge](https://github.com/DevStarByte/ByteForge) copies the PKGBUILDs from its
`packaging/arch-repo/` here with the new `pkgver`, and the **Build packages** workflow below builds and
signs them. A pre-release (`v*-beta.N`) updates `byteforge-beta`; a release (`vX.Y.Z`) updates both. Edit
those PKGBUILDs in ByteForge, not here, or the next release overwrites the change.

`byteforge-beta`'s `pkgver` drops the `-` (pacman doesn't allow it): `v1.1.0-beta.4` is
`pkgver=1.1.0beta.4`, which sorts before `1.1.0`. The two packages conflict; install one of them.

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
   To take a package out of the repo, delete its `pkgs/<name>/` folder.
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
