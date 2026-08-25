# Skills

Personal Agent Skills I use for software engineering and open-source project work. They are shared in case they help someone else, not as a polished community product. Expect the occasional rough edge.

Built on the [Agent Skills specification](https://agentskills.io/specification): each skill is self-contained, uses a standard `SKILL.md`, and keeps provider-specific integrations optional.

## Install

```bash
npx skills add hatemecha/skills --list      # browse
npx skills add hatemecha/skills             # install all
npx skills add hatemecha/skills --skill open-source-engineering
```

Use `-g` for a global install, or copy a skill directory into any Agent Skills-compatible runtime. See the [`skills` CLI](https://github.com/vercel-labs/skills) for agent targeting.

## Catalog

| Skill | Purpose |
| --- | --- |
| [`open-source-engineering`](./skills/open-source-engineering) | Design, implement, refactor, and review software with a bias toward simplicity and low technical debt. |
| [`open-source-project`](./skills/open-source-project) | Create, convert, audit, and prepare genuinely open-source projects. |
| [`orchestrating-engineering-agents`](./skills/orchestrating-engineering-agents) | Run multi-agent engineering work with bounded roles and evidence gates. |

Mention a skill by name to use it, e.g. *"Use the open-source-engineering skill to review this module and remove unnecessary complexity."*

## Foundations

These skills encode established practice, not invented rules. Each one leans on primary sources so you can verify the reasoning, and every skill lists the specific references it applies.

- **`open-source-engineering`** — applies well-known software design principles: [KISS](https://en.wikipedia.org/wiki/KISS_principle), [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it), [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns), [low coupling / high cohesion](https://en.wikipedia.org/wiki/Coupling_(computer_programming)), and measuring before [optimizing](https://en.wikipedia.org/wiki/Program_optimization).
- **`open-source-project`** — grounded in the canonical definitions of openness: [Open Source Definition](https://opensource.org/osd), [Free Software Definition](https://www.gnu.org/philosophy/free-sw.html), [Open Source AI Definition](https://opensource.org/ai/open-source-ai-definition), plus [SPDX](https://spdx.org/licenses/), [REUSE](https://reuse.software/spec/), [OpenSSF Best Practices](https://www.bestpractices.dev/), and [SemVer](https://semver.org/).
- **`orchestrating-engineering-agents`** — synthesizes standard engineering-process practice: [separation of duties](https://en.wikipedia.org/wiki/Separation_of_duties) between author and approver, [independent code review](https://en.wikipedia.org/wiki/Code_review), [least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) for workers, and an explicit [state machine](https://en.wikipedia.org/wiki/Finite-state_machine) as the control plane.

## License

[MIT](./LICENSE).
