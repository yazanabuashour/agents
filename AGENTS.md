# Personal agent profile

- `instructions/AGENTS.md` is the exported personal baseline; this file governs
  development of the profile, not other repositories.
- Own instructions, skills, and helpers usable from any harness, even if they
  call Pi. Pi-specific resources belong in [pi](https://github.com/yazanabuashour/pi).
  Leave machine settings, credentials, sessions, and application-owned skills
  with their owners.
- Keep profile installation and harness instruction wiring in `scripts/install-local`:
  discover exports and compatibility skill links without manual skill lists.
  Dotfiles owns machine preferences and explicit new-machine orchestration,
  not profile content, instruction links, or skill installation.
- Run `scripts/check` and preview installation with `scripts/install-local`
  (see [README.md](README.md)). Verify installed copies without reloading
  existing sessions.
- Preserve skill attribution and declared invocation policies across supported
  agents. Technical writing is eligible for automatic use; keep unrelated
  manual-only skills manual-only. Skill descriptions state only when to use them.
  Declare actual runtime dependencies rather than claiming backend independence.
