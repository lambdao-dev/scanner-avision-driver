# scanner-driver-avision

Arch Linux packaging for Avision's proprietary SANE backend, repackaged from
Avision's official Debian package into a native pacman package.

The package is mainly intended for the Avision FB5100, but the upstream driver
bundle contains backend modules and color profiles for many Avision scanners.

## Package Behavior

- Installs Avision's `avision_adv` SANE backend.
- Uses the libusb-1.0 backend variant shipped by Avision.
- Enables the backend through `/etc/sane.d/dll.d/avision_adv`.
- Installs Avision's udev rule for USB vendor ID `0638`.
- Stores vendor libraries under `/usr/lib/scanner-driver-avision`.
- Keeps `/opt/apps/scanner-driver-avision` as a compatibility symlink because
  Avision's binaries and config files may expect that upstream path.

## Building

Build the package with:

```sh
makepkg -si
```

The source tarball is downloaded from the Dropbox URL linked by Avision's
official product downloads page. `makepkg` verifies it with the SHA-256 checksum
recorded in `PKGBUILD`.

## Using the Scanner

After installing, unplug and replug the scanner so udev applies the shipped
permissions rule, then check SANE detection:

```sh
scanimage -L
```

If the scanner is not listed, check that the device is visible over USB:

```sh
lsusb
```

## Maintainer Notes

Regenerate `.SRCINFO` after changing `PKGBUILD`:

```sh
makepkg --printsrcinfo > .SRCINFO
```

Useful local checks:

```sh
makepkg --verifysource
makepkg -f
namcap PKGBUILD
namcap scanner-driver-avision-*.pkg.tar.zst
```

`namcap` reports many warnings about bundled ELF files lacking full RELRO. Those
libraries are proprietary upstream binaries, so the warnings cannot be fixed in
this package without an upstream rebuild.

## Source and License

Avision currently distributes this Linux driver as a Debian package inside a
tarball linked from their downloads page. The direct download target is Dropbox,
which is treated here as the official upstream source because it is what Avision
publishes.

Avision does not appear to ship a separate license file with this driver bundle.
The package therefore keeps `license=('custom')`, but there is no upstream
license text to install unless Avision provides one later.
