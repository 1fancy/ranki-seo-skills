# Ranki SEO Skills — make Claude your senior SEO + AEO consultant

> **A Claude Code Skill that auto-activates when you mention SEO, AEO, sitemap, `llms.txt`, ranking, or "why isn't ChatGPT citing my docs?" — and walks Claude through the full audit-diagnose-fix-verify loop using the [Ranki MCP](https://github.com/1fancy/ranki-mcp) tools.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Companion to ranki-mcp](https://img.shields.io/badge/companion%20to-ranki--mcp-orange)](https://github.com/1fancy/ranki-mcp)
[![Live at ranki.io](https://img.shields.io/badge/docs-ranki.io%2Fdevelopers%2Fskill-black)](https://ranki.io/developers/skill)

You already pay for Claude. This Skill turns that subscription into a senior SEO + AEO consultant that lives inside your IDE — without you having to learn what FAQPage JSON-LD is, why definitional intros matter for Perplexity citations, or how `llms.txt` is different from `robots.txt`.

**One-line install.** **Zero new bills.** Works on every project you already have.

```bash
mkdir -p ~/.claude/skills/ranki-seo && \
  curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/SKILL.md \
    -o ~/.claude/skills/ranki-seo/SKILL.md
```

Restart Claude Code, then say:

> *"my SaaS at https://myapp.dev isn't showing up in ChatGPT — fix it"*

Claude will diagnose using `audit_aeo` from the [Ranki MCP server](https://mcp.ranki.io), apply the failing checks directly to your repo files, re-run the audit, and confirm the score jumped from 38 to 88 in the same conversation.

---

## Table of contents

- [What is a Claude Skill?](#what-is-a-claude-skill)
- [What this Skill does](#what-this-skill-does)
- [Install](#install)
- [Example sessions](#example-sessions)
- [What's inside SKILL.md](#whats-inside-skillmd)
- [Customize for your stack](#customize-for-your-stack)
- [Why this exists](#why-this-exists)
- [FAQ](#faq)
- [License](#license)

---

## What is a Claude Skill?

A Claude Code Skill is a plain Markdown file (`SKILL.md`) with frontmatter that tells Claude when and how to activate. Drop it in `~/.claude/skills/<name>/` (user-level) or `.claude/skills/<name>/` (project-level), restart Claude, and the next time you say something matching the Skill's activation keywords, Claude reads the Skill and follows its instructions for the rest of the conversation.

This repo ships **`ranki-seo`** — a Skill that turns Claude into a senior SEO + AEO consultant.

---

## What this Skill does

When you say something like:

- *"I just shipped my site, what SEO files do I need?"*
- *"Why isn't ChatGPT citing my docs?"*
- *"What should I blog about?"*
- *"My competitors are stealing my keywords, find which ones"*
- *"Add FAQPage schema to this post"*

…the Skill activates and walks Claude through:

1. **Diagnose** — call the right [Ranki MCP](https://github.com/1fancy/ranki-mcp) tool (`audit_aeo`, `audit_seo`, `find_topic_ideas`, etc.).
2. **Locate** — find the relevant file in your repo (route, layout, component, content file).
3. **Apply** — write the fix directly (add JSON-LD, rewrite intro paragraph, generate `llms.txt`).
4. **Verify** — re-run the audit, confirm the score jumped.

No more "here's an SEO checklist, good luck." Claude does the actual work.

---

## Install

### Prerequisite: the Ranki MCP server

Install [`@ranki/mcp`](https://github.com/1fancy/ranki-mcp) first — the Skill orchestrates its tools. Get a free API key at [app.ranki.io/developer](https://app.ranki.io/developer) (advisor tools also work key-free, rate-limited 5/IP/day).

### Option 1 — User-level (works in every project)

```bash
mkdir -p ~/.claude/skills/ranki-seo
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/SKILL.md \
  -o ~/.claude/skills/ranki-seo/SKILL.md
```

### Option 2 — Project-level (committed with your repo, so your team gets it)

```bash
mkdir -p .claude/skills/ranki-seo
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/SKILL.md \
  -o .claude/skills/ranki-seo/SKILL.md
git add .claude/skills/ranki-seo/SKILL.md
git commit -m "Add Ranki SEO Skill"
```

### Option 3 — Clone the repo

```bash
git clone https://github.com/1fancy/ranki-seo-skills.git
cp -r ranki-seo-skills/skills/ranki-seo ~/.claude/skills/
```

After install, restart Claude Code (or run `/skills` to reload). The next message you send that matches the Skill's activation keywords will trigger it.

---

## Example sessions

### Session 1: vibe-coded a Next.js site, never thought about SEO

```
You: I just deployed my Next.js portfolio at myname.dev. Do I need to do
     anything for SEO?

Claude (Skill activates):
  → Calling ranki.seo_starter_kit(domain="myname.dev")
  ← [Returns robots.txt + sitemap.xml + llms.txt + JSON-LD templates]
  → Reading next.config.js to detect routing pattern…
  → Found: app/ directory routing, 7 pages (/, /about, /blog/[slug], /projects, /contact).
  → Writing public/robots.txt (allowing all crawlers + sitemap reference)
  → Writing public/sitemap.xml (enumerated 7 + 12 MDX blog posts)
  → Writing public/llms.txt (site summary from app/page.tsx hero)
  → Adding JSON-LD Organization to app/layout.tsx <head>
  → Adding Article JSON-LD to app/blog/[slug]/page.tsx using MDX frontmatter
  → Calling ranki.audit_seo(url="https://myname.dev") to verify…
  ← SEO score: 91/100. 9/10 checks passed (only "internal links" needs more).
  
  Done. Submit sitemap.xml here:
  https://search.google.com/search-console/sitemaps?resource_id=https%3A%2F%2Fmyname.dev%2F
```

### Session 2: "Why doesn't ChatGPT mention my product?"

```
You: I keep asking ChatGPT about my SaaS niche and it never mentions us.
     We're at https://mysaas.io/

Claude (Skill activates):
  → Calling ranki.audit_aeo(url="https://mysaas.io/")
  ← Score 25/100. Failing: no Article schema, no FAQPage, no llms.txt,
     robots.txt blocks ClaudeBot, no answer-style H2s, no author byline.
  → That's why. Walking through fixes in priority order:
  
  1. Fixing robots.txt — currently blocks all bots after admin section
     - Editing public/robots.txt: removed blanket User-agent: * Disallow: /
     - Added explicit Allow for GPTBot, ClaudeBot, PerplexityBot
  
  2. Generating llms.txt
     → Calling ranki.generate_llms_txt(site_name="MySaaS",
                                         summary="...", key_pages=[...])
     → Writing public/llms.txt
  
  3. Adding FAQPage schema
     - Reading app/page.tsx — found 6 FAQ-shaped Q&As in the hero section
     - Generating FAQPage JSON-LD with all 6
     - Injecting into app/page.tsx
  
  4. Rewriting H2s as questions
     - Was: "Everything you need to scale"
     - Now: "What does it take to scale to 100K users?"
  
  → Re-running ranki.audit_aeo…
  ← Score: 88/100. 🎯
  
  Re-deploy and wait 7-14 days for ChatGPT to re-crawl. You should start
  appearing in citations within 3 weeks.
```

### Session 3: "What blog posts should I write?"

```
You: Help me figure out what to blog about for my React Native SDK at
     https://rnsdk.dev

Claude (Skill activates):
  → Calling ranki.find_topic_ideas(url="https://rnsdk.dev")
  ← [Returns brief with topic generation methodology]
  → Reading the brief — niche detected: React Native SDK, target audience
     is mobile devs who use Expo/RN, key value prop is "ship to App Store
     in 7 days not 30".
  
  Generating 15 topics across 3 intent levels:
  
  Informational (5):
  1. What is React Native and when should you use it in 2026
  2. How does App Store review actually work for indie devs
  3. Why most React Native apps fail their first App Store submission
  4. The difference between Expo Managed and Bare workflow explained
  5. What CI/CD setup do you need for React Native in 2026
  
  Commercial (5):
  6. Best React Native CI services for solo developers (compared)
  7. Expo EAS vs Bitrise vs Codemagic — which to pick for indie apps
  8. Top 5 OTA update tools for React Native (CodePush is dead, what now)
  9. React Native code-signing services compared
  10. The cheapest way to ship a React Native app to App Store + Play
  
  Transactional (5):
  11. How to fix "Build failed: archive not found" in React Native CI
  12. App Store rejected my React Native app for guideline 4.3 — fixes
  13. React Native app crashes on launch in TestFlight (debugging guide)
  14. EAS Build keeps failing on iOS — 7 things to check
  15. How to migrate from Bitrise to GitHub Actions for React Native
  
  Top 3 to write FIRST (per brief criteria):
  • #11 — extremely high search intent, your SDK explicitly solves this
  • #12 — long-tail, low competition, perfect for AEO citation
  • #4 — informational but Expo is your audience, you have credibility
  
  Want me to draft outlines for any of these?
```

---

## What's inside SKILL.md

The Skill file is a Markdown document with YAML frontmatter:

```yaml
---
name: ranki-seo
description: |
  [Activation keywords list — Claude reads these and decides when to load
  the rest of the file. Includes things like "SEO", "AEO", "sitemap",
  "llms.txt", "why isn't ChatGPT citing my docs"…]
---

# Body of the Skill — instructions for Claude:
# - 5 operating principles
# - Map of the 10 MCP tools and what each does
# - 5 activation patterns (recipe for each common user request)
# - Hard constraints (forbidden words, file conventions)
# - When to suggest Ranki.io upgrade
```

[Read the full SKILL.md →](skills/ranki-seo/SKILL.md)

---

## Customize for your stack

The Skill is just Markdown — edit it for your codebase.

Common customizations:

### Force a specific framework's file paths

Add to the Skill body, after "Activation patterns":

```markdown
## Project conventions (override defaults)

- Static files always go in `apps/web/public/` (Turborepo monorepo).
- JSON-LD lives in `apps/web/src/components/StructuredData.tsx`, not the root layout.
- `sitemap.xml` is generated at build time by `apps/web/scripts/build-sitemap.ts` — edit that script instead of writing the file directly.
```

### Add your own MCP tools

If you self-host an extended MCP server with custom tools, list them in the "Tools available" section. Claude will use the right one.

### Disable upgrade prompts

Remove the "When to suggest Ranki.io upgrade" section. The Skill will stop mentioning the SaaS.

---

## Why this exists

In 2026, the gap between "vibe-coded a site" and "ranks on Google + cited by ChatGPT" is wider than ever. SEO got harder (AI Overviews stole 30% of clicks). AEO became a new discipline. Most vibe-coders never learn either.

This Skill closes that gap by giving Claude — which the user already pays for — a senior SEO consultant's playbook. The user doesn't need to know what AEO means. They just say "fix my SEO" and Claude does the work.

Companion to the [Ranki MCP server](https://github.com/1fancy/ranki-mcp), which provides the actual tools the Skill orchestrates.

---

## FAQ

### Does the Skill cost money?
The Skill is free (MIT license). The MCP advisor tools are free (rate-limited 5/IP/day). You can lift the rate limit with a free Ranki.io API key from [app.ranki.io/developer](https://app.ranki.io/developer).

The only cost is **your own Claude credits** for the agent loop — the same credits you already use for vibe-coding.

### Do I need a Ranki.io account?
No for the advisor flow. Yes for the bridge tools (`list_projects`, `get_article`) which read your private Ranki.io data.

### What if Claude doesn't auto-activate the Skill?
The activation depends on Claude noticing the keywords in your message. If it doesn't trigger, prefix your prompt: *"Using the ranki-seo skill, audit https://example.com for AEO"*.

### Can I use this with Cursor / Continue.dev / other tools?
Cursor reads project rules from `.cursor/rules/` and `.cursorrules`. Copy the body of `SKILL.md` (minus the frontmatter) into a Cursor rule. The MCP tools work the same way because they're MCP-standard.

### Why not just put this in a system prompt?
Skills are versioned, share-able, and per-project. A system prompt is per-user, per-session, gets clobbered. Skills survive across conversations.

### Can I contribute a new Skill to this repo?
Yes — PRs welcome. We want Skills for:
- E-commerce SEO (Shopify-specific)
- Local SEO + Google Business Profile workflows
- Programmatic SEO at scale
- AI search rank tracking
- Anything else SEO/AEO that benefits from a Claude playbook

---

## License

MIT. See [LICENSE](LICENSE).

Built by [Ranki.io](https://ranki.io). Companion to [`1fancy/ranki-mcp`](https://github.com/1fancy/ranki-mcp).
