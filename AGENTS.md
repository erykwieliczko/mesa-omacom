# Omacom Mesa assistant policy

This is the policy for AI coding assistants working in omacom/mesa. This file
is the only assistant instruction file the tree carries: `CLAUDE.md`,
`GEMINI.md` and `.github/copilot-instructions.md` are symlinks to it.
Upstream projects' instruction files are not carried here. To change the
policy, edit this file in a reviewed pull request.

## Rules

- Only public code goes in. Never copy code from proprietary sources.
- Preserve contributor identities, original commits, licenses and source
  provenance. Land imports with merge commits, not squashes or rebases.
- `integration/aurora` is the Apple Silicon integration branch. `main` and
  `26.2` mirror stock Mesa; never force-push or rewrite them.
- Changes land through a reviewed pull request with a green `ci / result`.
  CPU and drm-shim checks do not prove hardware correctness.
- Hardware qualification happens on the edge package channel, on the affected
  machines, before a change is promoted beyond edge. Record the exact source
  commit that was built and tested.
- Keep identity, firmware and kernel-ABI admission checks fail-closed. Do not
  enable an unqualified GPU generation or weaken admission to make one start.
- A source build must not install Mesa, change a boot stack or reboot a
  machine as a side effect.
