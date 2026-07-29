# Codex Skills

[![Validate skills](https://github.com/hatemecha/codex-skills/actions/workflows/validate-skills.yml/badge.svg)](https://github.com/hatemecha/codex-skills/actions/workflows/validate-skills.yml)

Personal Agent Skills for repeatable, high-quality work with Codex and other compatible coding agents.

## Quick install

Install a skill globally for Codex:

```bash
npx skills add hatemecha/codex-skills --skill open-source-project -g -a codex -y
```

Or install it with GitHub CLI:

```bash
gh skill install hatemecha/codex-skills skills/open-source-project
```

The GitHub CLI method requires `gh` 2.90.0 or later.

These commands will work after this repository is published as `hatemecha/codex-skills`.

## Skills

| Skill | What it does | Install |
| --- | --- | --- |
| [`ensayo-editor`](./skills/ensayo-editor) | Edits and audits Spanish essays while preserving the author's voice. | `npx skills add hatemecha/codex-skills --skill ensayo-editor -g -a codex -y` |
| [`open-source-project`](./skills/open-source-project) | Creates, converts, audits, and prepares genuinely open-source software projects. | `npx skills add hatemecha/codex-skills --skill open-source-project -g -a codex -y` |

## Usage

Skills can activate automatically when a request matches their description. You can also invoke one explicitly:

```text
Use $open-source-project to prepare this repository for its first public release.
```

### Ensayo Editor

Invoke it explicitly with a mode and paste a fragment or full essay:

```text
Usá $ensayo-editor en modo diagnóstico sobre este texto.
```

Available modes are diagnosis, minimal correction, artificial-voice audit, argumentative review, compared proposal, and clean version. A clean version is generated only when explicitly requested and uses only justified or previously approved changes.

The skill's [author voice profile](./skills/ensayo-editor/references/voz-del-autor.md) records the evidence level for every trait. No personal writing samples were available in this repository when the profile was created, so it relies on the author's explicit editorial preferences and keeps uncertain traits as hypotheses.

## Compatibility

The skills follow the open [Agent Skills specification](https://agentskills.io/specification). They use standard `SKILL.md`, `references/`, `scripts/`, and `assets/` conventions where applicable.

Some skills may also include `agents/openai.yaml` to improve their presentation in Codex. This metadata is optional for other compatible agents.

## Repository structure

```text
codex-skills/
├── skills/
│   ├── ensayo-editor/
│   │   ├── agents/
│   │   │   └── openai.yaml
│   │   ├── references/
│   │   └── SKILL.md
│   └── open-source-project/
│       ├── agents/
│       │   └── openai.yaml
│       ├── references/
│       └── SKILL.md
├── .github/
│   └── workflows/
│       └── validate-skills.yml
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

## Contributing

This is currently a personal, maintainer-led collection. Suggestions and focused pull requests are welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md) before adding or changing a skill.

## License

Available under the [MIT License](./LICENSE).
