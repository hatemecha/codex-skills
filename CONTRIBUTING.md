# Contributing

Thanks for helping improve this skill collection.

This is currently a personal, maintainer-led repository. Suggestions and pull requests are welcome, while final scope and design decisions remain with the maintainer.

## Add a skill

1. Create `skills/<skill-name>/`.
2. Use a lowercase, hyphenated name that matches the `name` in `SKILL.md`.
3. Add a concise `SKILL.md` with `name` and `description` in its YAML frontmatter.
4. Put activation guidance in the frontmatter description.
5. Add only the resources the skill needs:
   - `scripts/` for repeatable executable logic;
   - `references/` for documentation loaded on demand;
   - `assets/` for templates and output resources;
   - `agents/openai.yaml` for optional Codex interface metadata.
6. Reference every supporting file from `SKILL.md`.
7. Add the skill to the catalog in `README.md`.
8. Validate the collection before submitting.

Do not put a separate README, changelog, license, or other human-facing project documentation inside an individual skill folder.

## Validate locally

With GitHub CLI 2.90.0 or later:

```bash
gh skill publish . --dry-run
```

This validates all discovered skills without creating a release.

You can also verify discovery with the open skills CLI:

```bash
npx skills add . --list
```

## Pull requests

Keep changes focused and explain:

- the problem being solved;
- when the skill should activate;
- any new scripts, references, or assets;
- the validation performed.

Never commit credentials, personal data, generated secrets, or content you do not have permission to redistribute.
