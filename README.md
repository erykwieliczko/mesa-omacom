# Omacom Mesa

Mesa for Omarchy on Apple Silicon: Asahi and Zink OpenGL, and Honeykrisp
Vulkan. Mesa **26.3.0-devel**. The upstream Mesa README is
[README.rst](README.rst).

| Branch | Contents |
| --- | --- |
| `integration/aurora` | Aurora native GPU work plus Omacom integration fixes. Packages are meant to build from here. |
| `main`, `26.2` | Stock Mesa mirrors. Never force-pushed. |

The Aurora import brings in DJ's Neo/G17P work, Eryk Wieliczko's native M3 Pro
OpenGL/Vulkan work, Naeem's M3 Max additions and Scott Jones's M3 Air support,
with original commits, authors, copyrights and licenses kept.

## Hardware

| Hardware / contract | Status |
| --- | --- |
| M1/M2, AGX2 | Existing Asahi/Honeykrisp paths |
| M3 Pro, T6030 / G15S, legacy firmware with native AGX3 shaders | Native GL/Vulkan; boot and pixel checks passed on J516S |
| M3 Max, T6031 / G15C, single die / four clusters | Native admission; not yet tested on hardware here |
| M3 Air, T8122 / G15G, legacy 14.x firmware | Enabled by default for its exact identity and kernel contract; J613 desktop, texture, MSAA and compute checks [reported passing](https://github.com/omacom/omarchy-pkgs/pull/1591#issuecomment-6393944044); J615 not yet tested |
| Neo / A18 Pro, T8140 / G17P, HAL200 | DJ's AGX3b stack with its feature restrictions |
| M3 Ultra, M4, M5 | Refused |

A chip name or shader encoder alone does not establish support. Admission
checks the exact chip identity, topology, shader generation and kernel feature
contract, and fails closed on anything else.

## Kernel pairing

The native M3, M3 Air and Neo paths use the Aurora UAPI in
`include/drm-uapi/asahi_drm.h`, checked against
[omacom/linux-aurora at 7b969982](https://github.com/omacom/linux-aurora/tree/7b969982306e1498a8cbe6e5da9bde743acd8fc5).
That kernel's G15 backend still uses its older packet layout, so
`src/asahi/lib/agx_g15_wire.c` converts the exact G15S/G15C contract at the DRM
boundary and refuses unknown ones before VM creation. See
[SILICONFOREST-G15-WIRE.md](SILICONFOREST-G15-WIRE.md).

Do not substitute headers, force a chip identity or pair the driver with a
kernel whose version merely looks similar.

## Build and check

Pull requests run `ci / result`: CPU fixtures and kernel UAPI checks always,
plus x86_64 and aarch64 builds, every configured Meson test and drm-shim Vulkan
probes unless the PR only touches a short list of documentation files. See
[.github/mesa-ci/README.md](.github/mesa-ci/README.md). A push to
`integration/aurora` also produces an attested ARM staging archive. It is a
`debugoptimized` build with assertions enabled, not a package.

Local quick checks need a C compiler and Python 3:

```sh
python3 tools/siliconforest/check.py
```

A full build needs the Mesa toolchain and development libraries; on Ubuntu
26.04, [.github/mesa-ci/deps.sh](.github/mesa-ci/deps.sh) installs them. Then:

```sh
python3 -m venv ../mesa-venv
../mesa-venv/bin/pip install 'meson==1.10.2' 'Mako==1.3.10' 'ply==3.11' PyYAML pytest
MESON="$PWD/../mesa-venv/bin/meson" JOBS=8 tools/siliconforest/build.sh ../mesa-build
VK_INSTANCE_LAYERS=VK_LAYER_KHRONOS_validation \
  python3 tools/siliconforest/drm-smoke.py ../mesa-build
```

A source build installs nothing and changes no boot stack.

## Packaging

`mesa-aurora` and `vulkan-aurora` in
[omacom/omarchy-pkgs](https://github.com/omacom/omarchy-pkgs/tree/master/pkgbuilds/mesa-aurora)
pin an exact source commit. They still pin Aurora until
[omarchy-pkgs#1591](https://github.com/omacom/omarchy-pkgs/pull/1591) moves them
to `integration/aurora`. Hardware qualification happens on the edge channel.

## Provenance

The import was prepared in Chris Kearney's
[SiliconForest Mesa](https://github.com/chriskearney/siliconforest-mesa) fork,
which also keeps the compatibility and experimental source profiles
(`compat/*`, `experimental/*`). The review ledger, source inventory and test
receipts are kept as they were:

- [SILICONFOREST-REVIEW.md](SILICONFOREST-REVIEW.md): merged changes, duplicates, ABI findings and held work
- [SILICONFOREST-SOURCES.json](SILICONFOREST-SOURCES.json): pinned public source snapshots
- [SILICONFOREST-VALIDATION.md](SILICONFOREST-VALIDATION.md) and
  [SILICONFOREST-HARDWARE-20261024.json](SILICONFOREST-HARDWARE-20261024.json): build, host and hardware results
