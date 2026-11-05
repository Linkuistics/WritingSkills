# WritingSkills

WritingSkills is the writing pack of
[Grove](https://github.com/Linkuistics/grove). It ships Grove's editorial
session kinds, their shared procedure and two writing craft skills as agent
skills. Grove's core supplies the spine that each kind loads.

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

## Craft skills

| Skill | Purpose |
| --- | --- |
| `technical-communication` | Controlled-English writing rules, modelled on ASD-STE100, for all natural language that a model writes. Its always-on layers put the rules in front of the model in each session |
| `writing-code-walkthroughs` | Concept-ordered, source-exact code walkthroughs for Markdown books and subsystem guides |

A craft skill is guidance that a session of any kind can use. No Grove mandate
names it.

`technical-communication` holds more than its rules. Its directory also holds
these parts:

- `always-on/`: the instruction and the reminder that the hooks send.
- `scripts/`: the checker `ste-lint.py`, the hook core `tc_hook.py` and the
  hook guard `tc-hook`.
- `harnesses/`: the Codex hooks file and the Pi extension.
- `.claude-plugin/plugin.json` and `hooks/hooks.json`: the manifest and the
  hooks of the Claude Code hooks plugin.
- `.codex-plugin/plugin.json`: the manifest of the Codex hooks plugin.
- `LICENSES/`: the licence of the vendored checker.

## Hooks plugins

The always-on layers of `technical-communication` run as hooks. A hook is a
command that the harness runs at an event, such as the start of a session. Two
hooks plugins deliver them. Both plugins have the skill directory
`plugins/writing/craft/technical-communication` as their root, so each hook
runs `scripts/tc-hook` of that directory.

| Harness | Plugin | Manifest | Hooks file |
| --- | --- | --- | --- |
| Claude Code | `technical-communication-hooks` | `.claude-plugin/plugin.json` | `hooks/hooks.json`, which Claude Code reads by default |
| Codex | `technical-communication` | `.codex-plugin/plugin.json` | `harnesses/codex/hooks.json`, which the manifest names |

Neither plugin holds a skill. Ravel or the writing plugin delivers the skill
itself.

The Claude Code manifest lists `"skills": []`. Claude Code loads a plugin root
that holds `SKILL.md`, with no `skills/` directory and no `skills` key, as one
skill. The empty list stops that, so the hooks plugin adds no second
`technical-communication` skill. A Ravel user enables this plugin alone and
receives the hooks without a native copy of the skill.

The Codex manifest names its hooks file. Codex reads the default
`hooks/hooks.json` only when the manifest has no `hooks` field. The field
therefore keeps Codex away from the Claude Code hooks file in the same root.

The writing plugin lists `technical-communication-hooks` in its
`dependencies` and declares no hook. An installation of the writing plugin
therefore also installs the hooks plugin. A native user of the writing plugin
keeps the always-on layers, and each hook runs once, also when the user
installs both plugins. A bare dependency name resolves in the marketplace that
lists the writing plugin. That marketplace must list the hooks plugin too.

Codex adds a plugin only from a marketplace. The Codex marketplace file
`.agents/plugins/marketplace.json` at the repository root has the name
`writing-hooks`. It lists the Codex plugin with a `local` source, at the path
of the skill directory.

## Layout

The source directory is `plugins/writing`. It is the Claude Code plugin root
and the Ravel source directory. It has two roots:

- `skills` holds the four kind skills and the family skill.
- `craft` holds the two craft skills.

The directory `craft/technical-communication` is also the root of the two
hooks plugins in [Hooks plugins](#hooks-plugins).

The plugin manifest lists `"skills": ["./craft/"]`. Claude Code scans `skills/`
by default and adds `craft/`. `plugins/writing/PROVENANCE.md` holds the sources
of the craft skills.

The repository root holds the files that the pack does not deliver. They are
this README, the licence, the Taskfile and the documents in `docs/`. The Codex
marketplace file in `.agents/plugins/` and the scripts in `scripts/` are also
there. The scripts are the Pi load check and the hook count.

Grove's external pack contract, `docs/specs/external-pack-contract.md` in the
Grove repository, states the layout, the references between skills and the
conformance rules.

## Documents

These documents state, decide, explain or check the craft skills. They came
from Grove with the skills, at the same paths.

| Path | What it is |
| --- | --- |
| `docs/specs/technical-communication.md` | The specification of `technical-communication` |
| `docs/TECHNICAL-COMMUNICATION.md` | The user guide of `technical-communication` |
| `docs/design/technical-communication/` | The design views of the specification. `task design:technical-communication` serves them |
| `docs/adr/the-off-file-is-the-only-switch.md` | The decision that one off file turns off every always-on layer |
| `docs/adr/documents-are-gated-replies-are-measured.md` | The decision that each document write is checked and each reply is only scored |
| `docs/adr/technical-communication-carries-no-standard-text.md` | The decision that the skill carries no text of the standard |
| `docs/adr/behavioral-acceptance-is-closed-unmet.md` | The decision that closes the behavioural acceptance of `writing-code-walkthroughs` as unmet |
| `docs/technical-communication-publication-review.md` | The check of the skill's text against the notice of the standard |
| `docs/research/harness-hooks-a.md` | The survey of what each harness allows for hooks |
| `docs/evaluations/writing-code-walkthroughs/` | The behavioural evaluations of `writing-code-walkthroughs` and their evidence |

The finished evaluation records keep their bytes. Four scripts in the
evaluation directory read the skill, and they now name its path in this pack:
the three `harness.sh` files under `enabled/` and
`refinement-regression/template-test.sh`. The template test also reads the
plugin manifest of this pack.

## Install

Each route also needs Grove's core, which supplies the `grove` skill.

### Ravel

Add this record to the `skills.sources` list of the Ravel settings, beside the
record of Grove's core. Replace `<checkout>` with the absolute path of a
checkout of this repository:

```json
{ "id": "writing", "type": "local", "path": "<checkout>/plugins/writing", "roots": ["skills", "craft"] }
```

Ravel reads the checkout as a local directory. It does not fetch or update the
checkout. A tool outside Ravel holds the checkout at a published commit.

### Claude Code plugin

Install the plugin from Grove's marketplace:

```text
/plugin marketplace add Linkuistics/grove
/plugin install writing@linkuistics
```

The manifest declares the dependencies `grove` and
`technical-communication-hooks`. The installation therefore also adds Grove's
core and the hooks plugin. The skills then have the namespace `writing:`, for
example `writing:grove-draft` and `writing:technical-communication`.

To install only the always-on hooks, for example beside Ravel, name the hooks
plugin alone:

```text
/plugin install technical-communication-hooks@linkuistics
```

Grove's marketplace pins the first commit of this pack, which holds only the
editorial skills. The craft skills and the hooks plugin reach the marketplace
with the Grove change that removes Grove's copies of them. That change also
lists the hooks plugin, so the dependency of the writing plugin resolves.

### Codex hooks plugin

Add the marketplace of this repository and the plugin. Replace `<checkout>`
with the absolute path of a checkout of this repository:

```sh
codex plugin marketplace add <checkout>
codex plugin add technical-communication@writing-hooks
```

The plugin adds only the hooks. The skill reaches Codex through Ravel or
through Grove's manual installer.

### Manual installation

Grove's manual installer links the skills of both roots of a pack:

```sh
<grove checkout>/plugins/install.sh --pack <writing checkout>/plugins/writing
```

Until Grove removes its copies of the craft skills, Grove's `linkuistics`
plugin ships them too. The installer refuses a skill name that two scanned
plugins ship. It therefore does not install the craft skills of this pack
beside a Grove checkout that still ships them.

## Checks

Run every check of the pack:

```sh
task check
```

The conformance checks use Grove's conformance runner from a plain Git checkout
of Grove, under `.cache/grove/`. The `Taskfile.yml` variable `GROVE_COMMIT`
names that Grove commit. The variable `GROVE_REPOSITORY` names the public Grove
repository.

- `task check:plugin` checks both Claude Code plugins with
  `claude plugin validate`: the writing plugin, with the craft skills of
  `craft/`, and the hooks plugin. It skips when Claude Code is not installed.
- `task check:hooks` runs `scripts/hook-count.py`. The script counts the hook
  declarations of three installations: the writing plugin alone, the hooks
  plugin alone, and both. For each plugin, it reads the default
  `hooks/hooks.json`, the manifest's `hooks` field and the dependencies, as
  Claude Code does. Each installation must give one declaration for each of
  the six hooks. The script also checks the empty `skills` list and the hooks
  field of the Codex manifest. Its controls then mutate scratch copies of the
  pack, for example with a second declaration, and each control must fail.
  The `grove` plugin is a dependency of the writing plugin, and the task reads
  it from the Grove checkout.
- `task check:tc-checker` runs the self-test of the checker `ste-lint.py`.
- `task check:tc-hook-lint` runs ShellCheck on the hook guard `tc-hook` and on
  the Pi load check.
- `task check:tc-pi` loads the Pi extension in a real Pi, with no model
  request. It checks that the extension writes a start record. Its controls
  check that the off file stops that record and that Pi reports an extension
  that cannot load. It skips when Pi is not installed.
- `task check:conformance` checks the pack alone, then the aggregate of
  Grove's core and this pack. Conformance covers only the kind skills in
  `skills/`. It does not read `craft/`.

The three `check:tc-*` tasks are the checks of `technical-communication`. They
came from Grove's `scripts/check.sh` and need no Grove checkout.

`GROVE_COMMIT` names Grove commit `31ec9117c9dacfe993e80313addd432f2930d679`,
which removed Grove's own copies of the editorial skills. The pack passes
alone. The aggregate reads each editorial skill and each moved rule once.

## Tool requirements

| Skill or check | Tools |
| --- | --- |
| `technical-communication` | `python3`, version 3.9 or later, for the checker and the hook core. A POSIX `/bin/sh` for the hook guard `tc-hook` |
| `writing-code-walkthroughs` | None |
| The hooks plugins | `python3` and `/bin/sh`, as for `technical-communication`. Each hook runs `/bin/sh scripts/tc-hook` |
| `task check` | Bash, Git, Task, Python 3 and ShellCheck. Claude Code for `check:plugin`, and Pi for `check:tc-pi`. Each of those two skips when its tool is not installed |
| `task design:technical-communication:render` | D2 |

The scripts of the evaluation records name further tools, such as Codex, Ruby
and `jq`. They describe finished runs, and `task check` does not run them.

## Provenance

The editorial skills come from Grove commit
`f45c5a53796f2a5bf634fec340cb0d7aef63e184`, at these paths:

- `plugins/grove/skills/grove-draft/`
- `plugins/grove/skills/grove-copy-edit/`
- `plugins/grove/skills/grove-art/`
- `plugins/grove/skills/grove-proof/`
- `plugins/grove/skills/grove/references/editorial.md`, which is now
  `editorial-family/SKILL.md`

The craft skills and their documents come from Grove commit
`31ec9117c9dacfe993e80313addd432f2930d679`, at these paths:

- `plugins/linkuistics/skills/technical-communication/`, with every tracked
  file
- `plugins/linkuistics/skills/writing-code-walkthroughs/`
- The two sections of `plugins/linkuistics/PROVENANCE.md` for these skills,
  which are now in `plugins/writing/PROVENANCE.md`
- Each document in [Documents](#documents), at the same path
- `scripts/pi-extension-load.test.sh`

The two hooks plugins come from Grove commit
`31ec9117c9dacfe993e80313addd432f2930d679`, with these changes:

- `craft/technical-communication/hooks/hooks.json` is Grove's
  `plugins/linkuistics/hooks/hooks.json`. Its script path loses the part
  `skills/technical-communication/`, because the plugin root is now the skill
  directory.
- `.agents/plugins/marketplace.json` is Grove's file of the same path. Its name
  is `writing-hooks` in place of `linkuistics-hooks`, its display name is
  "Writing hooks", and its path names the skill directory in this pack.
- `craft/technical-communication/.claude-plugin/plugin.json` and
  `scripts/hook-count.py` are new.

Grove keeps the full history of these files. This repository imports none of
it.

Grove commit `31ec9117c9dacfe993e80313addd432f2930d679` removed Grove's copies
of the editorial files. `GROVE_COMMIT` names that commit, so the aggregate
check reads each editorial skill once.

The move changed the first two paragraphs of each kind skill. The first
paragraph is now the opening of Grove's external pack contract, and the second
names the `editorial-family` skill as the family procedure. The family file
gained frontmatter. Its body has one change: the phrase
"`references/execute.md`'s" became "the spine's". The family skill loads no
other skill, so a path into the spine cannot resolve from it.

The move of the craft skills made these changes:

- `technical-communication/.codex-plugin/plugin.json` names this repository.
  Its description names the routes that install the skill from this pack.
- `docs/TECHNICAL-COMMUNICATION.md` links the skill at its path in this pack.
  It links Grove's README by its public URL.
- `docs/adr/behavioral-acceptance-is-closed-unmet.md` links
  `plugins/writing/PROVENANCE.md`.
- `scripts/pi-extension-load.test.sh` and the four evaluation scripts name the
  skill's path in this pack.
- `plugins/writing/PROVENANCE.md` has its own opening paragraphs. Two paths in
  the section of `technical-communication` start with `craft/`.

No other text changed. The moved documents still describe the delivery through
Grove's `linkuistics` plugin, which is true until Grove removes its copies.

`technical-communication` vendors the checker of
[danyuchn/asd-ste100-skill](https://github.com/danyuchn/asd-ste100-skill),
which uses the MIT licence. Its own `LICENSES/` holds that licence text. The
editorial skills adapt no passage of a third party.

## Licence

WritingSkills uses the Apache License 2.0, as Grove does. The `LICENSE` file
holds the full text.
