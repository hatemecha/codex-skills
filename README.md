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
| [`open-source-project`](./skills/open-source-project) | Creates, converts, audits, and prepares genuinely open-source software projects. | `npx skills add hatemecha/codex-skills --skill open-source-project -g -a codex -y` |

## Usage

Skills can activate automatically when a request matches their description. You can also invoke one explicitly:

```text
Use $open-source-project to prepare this repository for its first public release.
```

## Compatibility

The skills follow the open [Agent Skills specification](https://agentskills.io/specification). They use standard `SKILL.md`, `references/`, `scripts/`, and `assets/` conventions where applicable.

Some skills may also include `agents/openai.yaml` to improve their presentation in Codex. This metadata is optional for other compatible agents.

## Repository structure

```text
codex-skills/
├── skills/
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
