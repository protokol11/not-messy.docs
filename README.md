# not-messy.docs

Agent skills that turn design conversations into specs, ADRs and a glossary, then sync the docs after implementation.

Docs are born in conversation and rot after code ships. An AI session discusses the design in chat, implements it, and leaves the docs frozen at what they said last week — the next session reads a stale spec and builds on wrong assumptions. This pack turns documentation into a pipeline, not an afterthought: the conversation becomes a spec, irreversible decisions become ADRs, drifting terms get a glossary, and after implementation the docs are regenerated to match what was built, not what was planned.

## The four skills

| Skill | Does |
|---|---|
| `to-spec` | Synthesizes the discussed context into an implementation-ready spec set: one spec per code seam, grouped into waves, gated by a verification checklist. Asks you to confirm the seam map before writing. |
| `to-adr` | Captures hard-to-reverse architectural choices with rejected alternatives, so they do not look arbitrary in six months. |
| `to-context` | Keeps the domain glossary (`CONTEXT.md`) precise and free of implementation details. |
| `update-docs` | After the verification commands ran: writes the session status, regenerates the spec and PRD indexes from spec frontmatter, moves finished PRDs to `.docs/PRDs/DONE/`. |

The index generator (`update-docs/scripts/docs-index.mjs`) is a reference implementation: it assumes one repo's layout (`.docs/status.json`, `.docs/SPECS/README.md`, `.docs/sessions/`) and hardcoded index rules, so outside that layout its manual steps port but `--write`/`--check` regeneration does not.

## Pipeline

```
conversation ──> to-spec ──> to-adr ───────┐
                    │          to-context ──┤
                    v                       v
               spec set (.docs/SPECS)   ADRs + glossary
                    │                       │
                    └──────────> implement ─┘
                                     │
                                     v
                              update-docs
   session status -> indexes (CURRENT_STATUS, SPECS/README, PRDs)
                              -> PRDs/DONE
```

1. You discuss a feature. `to-spec` turns the discussion into specs — one per seam, grouped into waves — and asks you to confirm the seam map before writing.
2. Hard-to-reverse decisions go to `to-adr`; resolved or conflicting terms go to `to-context`.
3. The agent implements the specs.
4. `update-docs` records the session, rebuilds the indexes from the same files the agent wrote, moves finished PRDs — after the verification commands ran, not before.

## Install

Copy the four skill folders into your agent skills directory:

```
~/.agents/skills/
  to-spec/
  to-adr/
  to-context/
  update-docs/
```

Works with any agent that reads `SKILL.md` files. In `pi`, invoke them as `/skill:to-spec`, `/skill:to-adr`, `/skill:to-context`, `/skill:update-docs`.

## Quick example

You finished a design discussion about a billing module. Invoke `/skill:to-spec`. It produces `.docs/SPECS/billing/refund-seam.md` (status `ready-for-agent`) with locked decisions, changed files, verification commands, and out-of-scope items. After implementation, run `/skill:update-docs`: it writes the session status, regenerates `.docs/CURRENT_STATUS.md` and `.docs/SPECS/README.md` from the sources, and moves the finished PRD under `.docs/PRDs/DONE/`.

## Contributing

File issues in the repository's issue tracker. Each skill folder carries its own `SKILL.md`; changes to the pack are verified by running the skills against a scratch repo and checking the regenerated indexes.

## License

MIT — see [LICENSE](LICENSE).
