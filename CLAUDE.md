# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose (two things living in one repo)

This repo is a fork of [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills) — an open-source **Agent Skills marketplace** for marketing tasks — that has also been repurposed as the working directory for a real client: **Elma Water Industries** (custom-labelled PET water bottles, Hyderabad). Know which one you're operating in before making changes:

1. **`skills/`, `tools/`, `.claude-plugin/`, `AGENTS.md`, `README.md`, `CONTRIBUTING.md`** — the upstream marketing-skills marketplace content and its Claude Code plugin manifest. Treat this as a content library: cross-agent compatible, no build step.
2. **`elma-website/`, `.agents/`, `vercel.json`** — the actual Elma business: a static marketing site deployed to Vercel, plus generated marketing artifacts (social calendars, outreach templates) produced *using* the skills in `skills/`.

Full skill-authoring conventions (frontmatter rules, naming, directory layout, PR checklist, tool registry) already live in **AGENTS.md** — read it before adding or editing anything under `skills/` or `tools/`. Don't duplicate those rules here.

## Commands

Skills and marketing artifacts are content-only — no build/lint/test tooling. What exists:

```bash
# Validate all skills' frontmatter against the Agent Skills spec
./validate-skills.sh            # local heuristic checker
./validate-skills-official.sh   # uses the official agentskills/skills-ref validator (needs network)

# Syntax-check / preview a tool CLI (zero-dependency Node 18+ scripts)
node --check tools/clis/<name>.js
node tools/clis/<name>.js                    # usage/help
node tools/clis/<name>.js <cmd> --dry-run    # preview request without sending
```

There is no dev server for `elma-website/` — it's a single static `index.html` (plus `robots.txt`, `sitemap.xml`, client logos). Open the file directly in a browser to preview changes.

CI (`.github/workflows/`) runs `validate-skill.yml` (official `Flash-Brew-Digital/validate-skill` action) on any changed `SKILL.md`, and `sync-skills.yml` regenerates `.claude-plugin/marketplace.json` + the README skills table from `skills/` on every push to `main` — don't hand-edit the `<!-- SKILLS:START -->…<!-- SKILLS:END -->` block in README.md or the `skills` array in `.claude-plugin/marketplace.json`; add/remove a skill directory instead and let the workflow sync it.

## Elma website architecture

`elma-website/index.html` is a single self-contained HTML file (no framework, no build step) with inline `<style>`/content sections in page order (hero, product/pricing, clients, testimonials, contact/WhatsApp CTA, etc.). It carries:

- JSON-LD `LocalBusiness`/`Offer` structured data in `<head>` — keep this in sync with any pricing, address, or contact changes made in the body.
- Open Graph / Twitter meta tags and a canonical tag — update alongside the `<title>`/description if page copy changes.

Deployment is via Vercel (`vercel.json`): `outputDirectory` is `elma-website`, no build/install command (static passthrough). `vercel.json` also force-redirects the `www` host to the apex domain.

All business facts (pricing, MOQ, contact numbers, client names, positioning, brand voice, target audience) are centralized in **`.agents/product-marketing-context.md`** — every marketing skill reads this first, and it's the source of truth to check before writing new copy, ads, or claims anywhere in the site or `.agents/` content. If you change something material about the business (pricing, contact info, service area), update this file too.

`.agents/` subfolders hold *generated marketing output* (not skill definitions): `instagram/` (content calendars, post schedules with image prompts), `outreach/` (email + WhatsApp templates), `retention/` (retention templates), `seo/` (Google Business Profile content). These are produced by running the skills in `skills/` against `product-marketing-context.md`.

## Working across the two halves

When a task is about the Elma business (copy, SEO, ads, social, outreach), first check `.agents/product-marketing-context.md` for facts, then reach for the relevant skill in `skills/` (e.g. `page-cro`, `copywriting`, `seo-audit`, `social-content`) for the workflow/framework to apply — the skills' own frontmatter `description` fields state when each applies. When a task is about the skills library itself (adding/editing a skill, tool integration doc, or the plugin marketplace), follow AGENTS.md's spec and don't reference Elma-specific content from generic skill files — they must stay cross-agent/cross-client reusable.
