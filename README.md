# WritingSkills

WritingSkills is the writing pack of
[Grove](https://github.com/Linkuistics/grove). It ships Grove's editorial
session kinds and their shared procedure as agent skills. Grove's core supplies
the spine that each kind loads.

## Kinds

| Kind | Skill | Purpose |
| --- | --- | --- |
| `draft` | `grove-draft` | Produces a document from its sources and its human-authored structure brief, and owns structure and technical truth |
| `copy-edit` | `grove-copy-edit` | Corrects sentences, terminology and consistency with the document's prose contract, without restructuring |
| `art` | `grove-art` | Makes the document's figures, in the medium that its contract admits |
| `proof` | `grove-proof` | Reads the whole document a final time, with every class in charter |

The four kinds are the stages of a document's publishing pipeline. They are one
family with one procedure. The family skill `editorial-family` holds that
procedure. Each kind loads the `grove` skill and then the `editorial-family`
skill. The family skill is not a kind, because its name has no `grove-` prefix.

## Layout

The source directory is `plugins/writing`. It is the Claude Code plugin root
and the Ravel source directory. Its only root is `skills`, which holds the four
kind skills and the family skill. The repository root holds the files that the
pack does not deliver: this README, the licence and the Taskfile.

A later step adds the writing craft skills in a `craft/` root and a second
plugin for the always-on hooks. Grove's task tree names that step
`writing-craft-pack-k181`.

Grove's external pack contract, `docs/specs/external-pack-contract.md` in the
Grove repository, states the layout, the references between skills and the
conformance rules.

## Install

Each route also needs Grove's core, which supplies the `grove` skill.

### Ravel

Add this record to the `skills.sources` list of the Ravel settings, beside the
record of Grove's core. Replace `<checkout>` with the absolute path of a
checkout of this repository:

```json
{ "id": "writing", "type": "local", "path": "<checkout>/plugins/writing", "roots": ["skills"] }
```

Ravel reads the checkout as a local directory. It does not fetch or update the
checkout. A tool outside Ravel holds the checkout at a published commit.

### Claude Code plugin

Install the plugin from Grove's marketplace:

```text
/plugin marketplace add Linkuistics/grove
/plugin install writing@linkuistics
```

The manifest declares the dependency `grove`, so the installation also adds
Grove's core. The skills then have the namespace `writing:`, for example
`writing:grove-draft`.

### Manual installation

Grove's manual installer links the skills of a pack:

```sh
<grove checkout>/plugins/install.sh --pack <writing checkout>/plugins/writing
```

Grove's marketplace entry for this pack arrives with the Grove release that
removes the editorial kinds from core. Until that release, Grove ships its own
copies of these kinds. The manual installer refuses a skill name that two
scanned plugins ship, so it refuses this pack beside such a Grove checkout.

## Checks

Run every check of the pack:

```sh
task check
```

The checks use Grove's conformance runner from a plain Git checkout of Grove,
under `.cache/grove/`. The `Taskfile.yml` variable `GROVE_COMMIT` names that
Grove commit. The variable `GROVE_REPOSITORY` names the public Grove
repository.

- `task check:conformance` checks the pack alone, then the aggregate of
  Grove's core and this pack.
- `task check:plugin` checks the Claude Code plugin with
  `claude plugin validate`. It skips when Claude Code is not installed.

`GROVE_COMMIT` names Grove commit `f45c5a53796f2a5bf634fec340cb0d7aef63e184`,
which still ships Grove's own copies of these skills. With that commit, the
pack passes alone. The aggregate fails, because it reads each editorial skill
and each moved rule twice. A later commit of this pack names the Grove commit
that removes Grove's copies, and its aggregate passes.

## Provenance

The skills come from Grove commit `f45c5a53796f2a5bf634fec340cb0d7aef63e184`,
at these paths:

- `plugins/grove/skills/grove-draft/`
- `plugins/grove/skills/grove-copy-edit/`
- `plugins/grove/skills/grove-art/`
- `plugins/grove/skills/grove-proof/`
- `plugins/grove/skills/grove/references/editorial.md`, which is now
  `editorial-family/SKILL.md`

Grove keeps the full history of these files. This repository imports none of
it.

The move changed the first two paragraphs of each kind skill. The first
paragraph is now the opening of Grove's external pack contract, and the second
names the `editorial-family` skill as the family procedure. The family file
gained frontmatter. Its body has one change: the phrase
"`references/execute.md`'s" became "the spine's". The family skill loads no
other skill, so a path into the spine cannot resolve from it. No other text
changed.

No moved file adapts a passage of a third party, so the pack has no
`LICENSES/` directory.

## Licence

WritingSkills uses the Apache License 2.0, as Grove does. The `LICENSE` file
holds the full text.
