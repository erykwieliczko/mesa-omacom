# Maintaining Omacom Mesa

`integration/aurora` is the integration branch. It is protected: changes land
through a pull request with a green `ci / result` and an approval of the latest
push, as a merge commit. Keep original contributor commits and licensing
intact; when a change needs adapting, use `git cherry-pick -x` and record how
the conflict was resolved.

## Taking a source update

1. Check that the source repository is public before pulling its refs or code
   in. This tree contains public code only.
2. Pin the exact source SHA. Compare it against `integration/aurora` and review
   dependencies and duplicate implementations. A newer topic branch is not proof
   that it includes another branch's fixes.
3. Keep identity, topology, USC, HAL and kernel-ABI admission checks intact.
   Hardware recognition and shader encoding alone do not establish support.
4. Update `SILICONFOREST-SOURCES.json` and the review ledger when the source
   inventory changes.

## Checking

CI builds both architectures and runs the host suite and drm-shim probes on
every non-documentation pull request. For changes around device setup,
mappings or synchronization, also read the drm-shim probe output, not just the
gate result. Changes to `.github/workflows/` or `.github/mesa-ci/` can change
what the gate checks, so review them as carefully as driver code.

## Shipping

Packages live in omacom/omarchy-pkgs (`pkgbuilds/mesa-aurora`). To ship a
change, pin the exact merged `integration/aurora` commit there, build the split
packages and release on edge. Qualification on the affected machines happens on
edge, before promotion beyond it. CPU tests and drm-shim checks do not replace
that.
