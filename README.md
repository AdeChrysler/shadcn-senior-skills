# shadcn-senior-skills

Agent skills for building UI the senior way with [shadcn/ui](https://ui.shadcn.com): own the source, theme with semantic OKLCH tokens first, extend with variants and product wrappers — not panic `className`s.

Inspired by Jan Marshal’s [How Senior Engineers Use shadcn/ui](https://youtu.be/sNjFA7-_8k8).

## Skills

- **[shadcn-design-system](skills/shadcn-design-system/SKILL.md)** — Design-system architecture for owned-source UI kits: tokens → variants → wrappers → rare one-off `className`.

This is complementary to polish skills (e.g. better-ui) and motion skills (e.g. emil-design-eng). Run those *after* the foundation is correct.

## Install

```bash
npx skills add AdeChrysler/shadcn-senior-skills
```

Or add only this skill:

```bash
npx skills add AdeChrysler/shadcn-senior-skills --skill shadcn-design-system
```

## When to use

Use `shadcn-design-system` when you are:

- Setting up or changing a shadcn theme
- Customizing buttons/cards/forms without forking the whole look
- Cleaning up screens that look like stock shadcn demos or random Tailwind palette soup
- Reviewing UI PRs for design-system consistency

## License

MIT
