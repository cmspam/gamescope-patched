# gamescope-patched

A pacman repository carrying Arch's `gamescope` rebuilt for x86-64-v3, for use on CachyOS.

CachyOS ships its own `gamescope` in `cachyos-v3`, and that repo sits above `extra` in pacman's search order, so it is what a CachyOS machine installs. It tracks Arch at a distance of several weeks. This repository builds the current Arch packaging tag against the `cachyos/cachyos-v3` toolchain so a CachyOS system can run a gamescope that is up to date.

The build applies no patches. The name is kept because the repository URL is referenced by existing `pacman.conf` entries.

## Setup

Add to `/etc/pacman.conf`, above the `[cachyos-v3]` line:

```ini
[gamescope-patched-v3]
SigLevel = Optional TrustAll
Server = https://github.com/cmspam/gamescope-patched/releases/download/latest-v3
```

Then:

```bash
sudo pacman -Syu gamescope
```

The packages are unsigned, so `SigLevel = Optional TrustAll` is required.

## How it works

A scheduled run each day:

1. reads the newest packaging tag from [Arch's `gamescope` repository](https://gitlab.archlinux.org/archlinux/packaging/packages/gamescope),
2. fetches the PKGBUILD at that tag,
3. builds it in `cachyos/cachyos-v3` with `-march=x86-64-v3 -O3`,
4. publishes the result to the `latest-v3` release, and to a release tagged with the version.

The rebuild runs daily even when the version has not changed, because a prebuilt binary on rolling Arch goes stale as library sonames move.

`SOURCE_DATE_EPOCH` is pinned to the packaging tag's commit time, so two builds of the same tag against the same toolchain produce the same bytes.

## License

The packaging is Arch's and keeps its upstream license. The workflow is GPL-2.0.
