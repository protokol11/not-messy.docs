# not-messy.docs

Skills that keep project docs honest: conversation in, spec + ADR + glossary out, docs updated after implementation.

- **Turn conversation into a spec** → `to-spec` synthesizes what you already discussed into an implementation-ready spec set, split by code seams and gated by a verification checklist.
- **Record decisions and terms** → `to-adr` captures hard-to-reverse architectural choices; `to-context` keeps the domain glossary precise and free of implementation details.
- **Keep docs from rotting** → `update-docs` writes session statuses, regenerates the spec and PRD indexes, and moves finished PRDs to DONE — after you run the verification commands, not before.

## Why

AI coding sessions move fast. The design discussion happens in chat, the agent implements it, and the docs stay frozen at whatever they said last week. Three weeks later a new session reads a stale spec and builds on the wrong assumptions.

The fix is to treat documentation as a pipeline, not an afterthought. First the conversation becomes a spec: six questions answered, seams mapped, acceptance criteria testable. Decisions that would look arbitrary in six months become ADRs with rejected alternatives. Terms that drift get sharpened in a glossary. After implementation, the docs are updated to match what was built — not what was planned.

So docs do not rot after the code ships. The spec index, the PRD index, and the current status are regenerated from the same files the agent writes during the work.

## How it works

```
conversation ──> to-spec ──> to-adr ───────┐
                    │          to-context ──┤
                    v                       v
               spec set (.docs/SPECS)    ADRs + glossary
                    │                       │
                    └──────────> implement ─┘
                                     │
                                     v
                              update-docs
   session status -> indexes (CURRENT_STATUS, SPECS/README, PRDs)
                              -> PRDs/DONE
```

1. You discuss a feature. `to-spec` synthesizes the context into specs — one spec per seam, grouped into waves — and asks you to confirm the seam map before writing.
2. Decisions that are hard to reverse go to `to-adr`; resolved or conflicting terms go to `to-context` (`CONTEXT.md`).
3. The agent implements the specs.
4. `update-docs` records the session, rebuilds the indexes from spec frontmatter and status files, moves finished PRDs, and commits the doc paths — but only after the verification commands ran.

## Install

Copy the four skill folders into your agent skills directory:

```
~/.agents/skills/
  to-spec/
  to-adr/
  to-context/
  update-docs/
```

Works with any agent that reads SKILL.md files. In `pi`, invoke them as `/skill:to-spec`, `/skill:to-adr`, `/skill:to-context`, `/skill:update-docs`.

## Quick example

You finished a design discussion about a billing module. Say:

```
/skill:to-spec
```

It produces `.docs/SPECS/billing/refund-seam.md` (status `ready-for-agent`) with locked decisions, changed files, verification commands, and out-of-scope items. During the follow-up, say `/skill:to-adr` on the decision to store refunds as events. After implementation, run `/skill:update-docs` — it writes the session status, regenerates `.docs/CURRENT_STATUS.md` and `.docs/SPECS/README.md` from the sources, and moves the finished PRD under `.docs/PRDs/DONE/`.

## Honest status

- `to-spec`, `to-adr`, `to-context`: working, used on real projects.
- `update-docs`: **partially project-specific.** Its index generator, `update-docs/scripts/docs-index.mjs`, exists and runs, but it embeds conventions from one repo: it requires `.docs/status.json`, `.docs/SPECS/README.md`, and `.docs/sessions/` to exist, and its index section rules are hardcoded to that repo's areas and streams. Outside a repo with the same layout, the manual steps (status file, PRD index, DONE moves) still work; the `--write`/`--check` regeneration does not port as-is. Treat the script as a reference implementation, not a universal tool.

## License

MIT — see [LICENSE](LICENSE).
