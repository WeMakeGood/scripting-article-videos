# Working on this skill repo

This file is read by Claude Code when working **on** the skill (editing it, versioning it, releasing it). It is not read when the skill is *invoked* — that's what SKILL.md is for.

## What this repo is

A standalone Make Good Claude Code skill, distributed as both a ZIP from this repo's Releases page and as part of the [makegood-skills aggregator plugin](https://github.com/WeMakeGood/makegood-skills).

## Source of truth

The meaningful content of this skill is:

- `SKILL.md` — the skill instructions Claude Code loads when the skill is invoked
- `references/`, `scripts/`, `templates/` — supporting files SKILL.md references at runtime
- `examples/` — representative output for users

These are what users install and what gets vendored into the aggregator. Everything else (`README.md`, `CHANGELOG.md`, `LICENSE`, `.github/`, `.gitignore`) is downstream scaffolding — important for repo hygiene, but not what makes the skill work.

## Foundation standards

@~/.claude/CLAUDE.md applies. The behavioral standards in F0 (sourcing, marking the move, reframing, second-order checks, generalization) govern any substantive edits to SKILL.md or references.

When editing skill content, treat SKILL.md as a metaprompt — every sentence should change agent behavior, not describe behavior. If a sentence could be removed without changing what the agent does, it should be removed.

## Release workflow

When SKILL.md or supporting files change meaningfully:

1. Update the relevant files
2. Add a new `## [<version>] — <YYYY-MM-DD>` section to `CHANGELOG.md` describing what changed and why
3. Commit with a message focused on the change to skill behavior, not the file edits
4. Tag the release: `git tag -a v<version> -m "Release v<version>" && git push origin v<version>`
5. The release workflow in `.github/workflows/release.yml` builds a ZIP and creates a GitHub release with notes from the CHANGELOG section

Version bumps:
- **Patch** (e.g., 1.0.1 → 1.0.2): typo fixes, doc-only edits, no behavioral change
- **Minor** (e.g., 1.0.0 → 1.1.0): new capability, expanded triggers, additional reference files
- **Major** (e.g., 1.0.0 → 2.0.0): breaking change to how the skill activates, output structure, or required inputs

After tagging here, the aggregator at [WeMakeGood/makegood-skills](https://github.com/WeMakeGood/makegood-skills) needs to be updated separately to pick up the new version (edit `skills.yaml`, run `sync_skills.py`, bump aggregator version).

## What not to do

- **Don't edit content in the aggregator's vendored copy.** Vendored skills under `plugins/makegood-skills/skills/` in the aggregator repo are downstream artifacts. Edits there are overwritten on next sync. Always edit here.
- **Don't reorganize the repo structure** without updating the release workflow's rsync excludes. The structure matches what the aggregator and standalone-ZIP-install both expect.
- **Don't bump the version without a CHANGELOG entry.** The release workflow extracts notes from the matching `## [<version>]` section; missing the section means the release ships with a placeholder.
- **Don't reference files outside this repo from SKILL.md.** Each skill is atomic. If SKILL.md mentions content from another skill, vendor it or rephrase as guidance the agent can act on without the external file.

## Make Good context

Voice, content standards, organizational identity, and methodology references for substantive content edits live in `~/.claude/makegood-context-library/`. Load relevant agent modules from there when working on skill content that has voice or strategy implications (e.g., descriptions, example outputs, README polish).
