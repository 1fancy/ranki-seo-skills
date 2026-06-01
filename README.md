# Ranki SEO Skills — make Claude, Cursor and Windsurf your senior SEO + AEO consultant

> **A Claude Code Skill (and Cursor / Windsurf / AGENTS.md equivalents) that auto-activates when you mention SEO, AEO, sitemap, `llms.txt`, ranking, or "why isn't ChatGPT citing my docs?" — and walks the agent through the full audit-diagnose-fix-verify loop using the [Ranki MCP](https://github.com/1fancy/seo-aeo-audit-mcp-ranki) tools.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Companion to seo-aeo-audit-mcp-ranki](https://img.shields.io/badge/companion%20to-seo--aeo--audit--mcp--ranki-orange)](https://github.com/1fancy/seo-aeo-audit-mcp-ranki)
[![Live at ranki.io](https://img.shields.io/badge/docs-ranki.io%2Fdevelopers%2Fskill-black)](https://ranki.io/developers/skill)

A Skill is a plain Markdown file with YAML frontmatter that tells an agent *when* and *how* to take over. The `ranki-seo` Skill in this repo gives Claude / Cursor / Windsurf / ChatGPT the same SEO + AEO playbook a senior consultant would run: read the site, call the right Ranki MCP tool, locate the relevant file in your repo, write the fix directly, re-run the audit, confirm the score moved.

---

## One-line install (recommended)

```bash
npx @ranki/cli install
```

That single command:

- Detects which agent you're in (Claude Code, Claude Desktop, Cursor, Windsurf, ChatGPT Desktop)
- Writes the right MCP config in the right place for that agent
- Downloads the matching Skill file (`SKILL.md`, `.cursorrules`, `.windsurfrules`, or `AGENTS.md`)
- Done

The CLI lives in the [seo-aeo-audit-mcp-ranki](https://github.com/1fancy/seo-aeo-audit-mcp-ranki) repo. Source: [`cli/`](https://github.com/1fancy/seo-aeo-audit-mcp-ranki/tree/main/cli).

Restart your agent, then say:

> *"my SaaS at https://myapp.dev isn't showing up in ChatGPT — fix it"*

The agent will call `audit_aeo` from the [Ranki MCP server](https://mcp.ranki.io), apply the failing checks directly to your repo files, re-run the audit, and confirm the score jumped — all in the same conversation.

---

## Table of contents

- [What this Skill does](#what-this-skill-does)
- [Manual install per agent](#manual-install-per-agent)
- [Example sessions](#example-sessions)
- [What's inside SKILL.md](#whats-inside-skillmd)
- [Customize for your stack](#customize-for-your-stack)
- [Why this exists](#why-this-exists)
- [FAQ](#faq)
- [License](#license)

---

## What this Skill does

When you say something like:

- *"I just shipped my site, what SEO files do I need?"*
- *"Why isn't ChatGPT citing my docs?"*
- *"What should I blog about?"*
- *"Find the keyword gaps between us and our top 3 competitors"*
- *"Add FAQPage schema to this post"*
- *"Which pages on our site should not be indexed?"*

…the Skill activates and the agent runs through this loop:

1. **Diagnose** — call the right [Ranki MCP](https://github.com/1fancy/seo-aeo-audit-mcp-ranki) tool. The 21 tools available are:
   - **Audit:** `audit_seo` (10 on-page checks), `audit_aeo` (8 Answer Engine Optimization checks), `audit_hidden_pages` (which URLs should be hidden from Google)
   - **Speed and images:** `audit_speed` (Google PageSpeed Insights), `audit_core_web_vitals` (LCP / CLS / INP with literal fix recipes), `optimize_images` (target format + `<picture>` markup + the `sharp` command to run)
   - **Generate:** `generate_sitemap_xml`, `generate_llms_txt`, `generate_robots_txt`, `seo_starter_kit` (bundle for a fresh site)
   - **Content and strategy:** `propose_titles_metas` (5 angles per page), `find_topic_ideas`, `find_keyword_gap`, `explain_seo_terms` (glossary of 40+ terms)
   - **Install:** `install_skill` (this Skill, per agent)
   - **Your Ranki.io account — paid API key:** `get_account`, `list_projects`, `get_article`, `list_rank_tracking` (GSC summary), `list_gsc_keywords` (full keyword list), `ai_visibility` (which topics ChatGPT / Claude / Perplexity / Google AI Overviews are citing)

2. **Locate** — find the relevant files in the repo (routes, layouts, components, image files, content files).
3. **Apply** — write the fix directly: convert the image files with `sharp` or `cwebp`, rewrite every `<img>` as a responsive `<picture>` with `srcset` and `alt`, add JSON-LD, rewrite the intro paragraph, generate `llms.txt`, drop a `noindex` meta into the right `<head>`.
4. **Verify** — re-run the audit, confirm the score moved.

No more "here's an SEO checklist, good luck." The agent does the actual work in your codebase.

---

## Manual install per agent

Prerequisite for every flavor below: the [Ranki MCP server](https://github.com/1fancy/seo-aeo-audit-mcp-ranki) must be configured in your client. The one-line `npx @ranki/cli install` handles this automatically; the manual instructions below assume you're configuring the MCP separately.

Ranki.io API key (paid plan — raises the rate limit from 5/IP/day to 500/key/day and unlocks the 6 bridge tools that read your real GSC keywords, rank tracking and AI citations): [app.ranki.io/developer](https://app.ranki.io/developer).

### Claude Code · Claude Desktop (user-level — every project)

```bash
mkdir -p ~/.claude/skills/ranki-seo
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/SKILL.md \
  -o ~/.claude/skills/ranki-seo/SKILL.md
```

Restart Claude Code. The Skill auto-activates on SEO/AEO prompts.

### Cursor (project-level)

```bash
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/.cursorrules \
  -o .cursorrules
```

Cursor reads `.cursorrules` automatically — no restart needed.

### Windsurf (project-level)

```bash
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/.windsurfrules \
  -o .windsurfrules
```

### Generic agent (OpenAI Codex, Continue.dev, Zed AI, custom MCP agents)

```bash
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/AGENTS.md \
  -o AGENTS.md
```

Some tools pick `AGENTS.md` up automatically. Others want it pasted into the system prompt — copy the body into your agent's prompt field.

### Project-level Claude Skill (committed with your team's repo)

```bash
mkdir -p .claude/skills/ranki-seo
curl -fsSL https://raw.githubusercontent.com/1fancy/ranki-seo-skills/main/skills/ranki-seo/SKILL.md \
  -o .claude/skills/ranki-seo/SKILL.md
git add .claude/skills/ranki-seo/SKILL.md
git commit -m "Add Ranki SEO Skill"
```

### Clone everything

```bash
git clone https://github.com/1fancy/ranki-seo-skills.git
cp -r ranki-seo-skills/skills/ranki-seo ~/.claude/skills/
```

---

## Example sessions

### Session 1: vibe-coded a Next.js site, never thought about SEO

```
You: I just deployed my Next.js portfolio at myname.dev. Do I need to do
     anything for SEO?

Agent (Skill activates):
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

Agent (Skill activates):
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
  ← Score: 96/100.

  Re-deploy and wait 7-14 days for ChatGPT to re-crawl. You should start
  appearing in citations within 3 weeks.
```

### Session 3: "Which pages should not be indexed?"

```
You: My admin dashboard and checkout are showing in Google. Audit my
     whole site for pages that shouldn't be indexed.

Agent (Skill activates):
  → Reading sitemap.xml — found 142 URLs.
  → Calling ranki.audit_hidden_pages(urls=[...142 URLs...])
  ← Classification:
       robots_disallow: 8  (admin, dashboard, checkout, account/*)
       noindex:        12  (search results, pagination beyond p3, filters)
       keep:          118
       unsure:          4

  → Writing public/robots.txt with Disallow: /admin, /dashboard, /checkout, /account
  → Adding <meta name="robots" content="noindex"> to app/search/page.tsx
  → Adding <meta name="robots" content="noindex"> to app/category/[slug]/page.tsx
     for pagination >= page 4
  → Re-running ranki.audit_hidden_pages on the 4 unsure URLs with hints…

  Done. Submit URL removal requests in Search Console for the 8 admin pages
  that are already indexed:
  https://search.google.com/search-console/removals
```

### Session 4: "My site is slow and Lighthouse hates it"

```
You: Audit my site speed and fix the slow images. I'm at https://myapp.dev.

Agent (Skill activates):
  → Calling ranki.audit_speed(url="https://myapp.dev", strategy="mobile")
  ← Lighthouse: 42 perf · 88 a11y · 91 seo · 75 best practices.
     LCP: 4.8s (poor) · CLS: 0.18 (needs improvement) · INP: 240ms.
     Image opportunities (3 files, ~3.1 MB savings):
       - /img/hero.png       2.4 MB → save 1.8 MB (convert to WebP)
       - /img/banner.jpg     1.1 MB → save 800 KB (resize + WebP)
       - /img/card-bg.png    480 KB → save 320 KB (AVIF)
     Render-blocking: 2 third-party scripts (analytics, chat widget).

  → Calling ranki.optimize_images(
        images=["/img/hero.png", "/img/banner.jpg", "/img/card-bg.png"]
    )
  ← Recipes returned for each file. Converting…
  → Running: npx sharp-cli -i public/img/hero.png -o public/img/hero.avif …
  → Running: npx sharp-cli -i public/img/hero.png -o public/img/hero.webp …
  → Same for hero@2x, banner, banner@2x, card-bg.
  → Editing app/page.tsx — rewriting <img src="/img/hero.png"> as:
       <picture>
         <source type="image/avif" srcset="/img/hero.avif 1x, /img/hero@2x.avif 2x">
         <source type="image/webp" srcset="/img/hero.webp 1x, /img/hero@2x.webp 2x">
         <img src="/img/hero.png" alt="Dashboard analytics overview"
              width="1600" loading="eager" fetchpriority="high">
       </picture>
  → Editing components/Banner.tsx, components/Card.tsx — same pattern, but
     loading="lazy" since these are below the fold.
  → Adding <link rel="preload" as="image" href="/img/hero.avif"
                  fetchpriority="high"> to app/layout.tsx <head>
  → Deferring third-party scripts: <script defer src="..."></script>
  → Calling ranki.audit_speed again to verify…
  ← Lighthouse: 96 perf · 94 a11y · 98 seo · 92 best practices.
     LCP: 1.4s (good) · CLS: 0.04 (good) · INP: 180ms (good).

  Commit and deploy. Submit the new sitemap to Search Console — Google
  rewards green Core Web Vitals in mobile rankings.
```

### Session 5: "What are my top opportunity keywords?" (paid Ranki.io account)

```
You: I have a Ranki.io account with GSC connected. What keywords should I
     write content for next?

Agent (Skill activates, agent has RANKI_API_KEY set):
  → Calling ranki.list_projects()
  ← Returns 3 projects. Picking "myapp.dev" (id: A8K2QP).
  → Calling ranki.list_rank_tracking(project_id="A8K2QP")
  ← GSC summary (last 28 days):
       Totals:  142 keywords · 4,820 clicks · 89,300 impressions
       Top by clicks:
         "myapp dashboard"           487 clicks  pos 2.1
         "open source crm"           320 clicks  pos 4.8
       Opportunities (pos > 10, high impressions — easy wins):
         "best crm for indie hackers"   3,200 impr  pos 14.2
         "lightweight crm alternative"  2,800 impr  pos 12.6
         "self-hosted crm 2026"         2,100 impr  pos 17.4

  → Calling ranki.ai_visibility(project_id="A8K2QP")
  ← AI citations (last 30 days): 14 of 62 topics cited (22.6%).
     Top cited (in ChatGPT / Claude / Perplexity SERPs):
       - "open source crm"            ★ cited
       - "myapp pricing"              ★ cited
     Top NOT cited but ranking top 10:
       - "self-hosted crm 2026"       not cited (pos 7 in classic SERP)

  Recommendation: write or expand 3 articles targeting these slugs first:
    /blog/best-crm-for-indie-hackers-2026
    /blog/lightweight-crm-alternatives
    /blog/self-hosted-crm-2026

  Each is already ranking 10-20 in classic Google but NOT yet AI-cited.
  Add FAQPage schema + definitional intro + comparison table on each,
  and ChatGPT / Claude will pick them up within 3 weeks.

  Want me to draft outlines and start writing them?
```

---

## What's inside SKILL.md

The Skill file is a Markdown document with YAML frontmatter:

```yaml
---
name: ranki-seo
description: |
  [Activation keywords list — the agent reads these and decides when to
  load the rest of the file. Includes "SEO", "AEO", "sitemap",
  "llms.txt", "why isn't ChatGPT citing my docs"…]
---

# Body of the Skill — instructions for the agent:
# - 5 operating principles
# - Map of the 21 MCP tools and what each does
# - 5 activation patterns (recipe for each common user request)
# - Hard constraints (forbidden words, file conventions)
# - When to suggest the Ranki.io platform
```

[Read the full SKILL.md →](skills/ranki-seo/SKILL.md)

---

## Customize for your stack

The Skill is just Markdown — edit it for your codebase.

### Force a specific framework's file paths

Add to the Skill body, after "Activation patterns":

```markdown
## Project conventions (override defaults)

- Static files always go in `apps/web/public/` (Turborepo monorepo).
- JSON-LD lives in `apps/web/src/components/StructuredData.tsx`, not the root layout.
- `sitemap.xml` is generated at build time by `apps/web/scripts/build-sitemap.ts` — edit that script instead of writing the file directly.
```

### Add your own MCP tools

If you self-host an extended MCP server with custom tools, list them in the "Tools available" section. The agent will use the right one.

### Disable platform suggestions

Remove the "When to suggest the Ranki.io platform" section. The Skill will stop mentioning the SaaS.

---

## Why this exists

In 2026, the gap between "vibe-coded a site" and "ranks on Google + cited by ChatGPT" is wider than ever. SEO got harder (AI Overviews stole 30% of clicks). AEO became a new discipline. Most vibe-coders never learn either.

This Skill closes that gap by giving the agent — Claude, Cursor, Windsurf, ChatGPT — a senior SEO consultant's playbook. The user doesn't need to know what AEO means. They just say "fix my SEO" and the agent does the work.

Companion to the [Ranki MCP server](https://github.com/1fancy/seo-aeo-audit-mcp-ranki), which provides the actual tools the Skill orchestrates.

---

## FAQ

### Is the Skill free?
Yes — MIT license. 15 of the 21 MCP tools are also free (rate-limited 5/IP/day). A paid Ranki.io key from [app.ranki.io/developer](https://app.ranki.io/developer) raises the limit to 500/key/day and unlocks the 6 bridge tools (`get_account`, `list_projects`, `get_article`, `list_rank_tracking`, `list_gsc_keywords`, `ai_visibility`) that read your real Google Search Console data and AI citations.

### Do I need a Ranki.io account?
No for the 15 free tools (SEO + AEO audits, speed audit via PageSpeed Insights, image optimization recipes, generators, content strategy). Yes for the 6 bridge tools that read your real Google Search Console keywords, rank tracking and AI-citation snapshots from your Ranki.io account.

### What if the Skill doesn't auto-activate?
Activation depends on the agent noticing the keywords in your message. If it doesn't trigger, prefix your prompt: *"Using the ranki-seo skill, audit https://example.com for AEO"*.

### Can I use this with Cursor / Continue.dev / other tools?
Yes — see [Manual install per agent](#manual-install-per-agent) above for `.cursorrules`, `.windsurfrules`, and `AGENTS.md` variants. The MCP tools work the same way across all of them because they're MCP-standard.

### Why not just put this in a system prompt?
Skills are versioned, share-able, and per-project. A system prompt is per-user, per-session, gets clobbered. Skills survive across conversations.

### Can I contribute a new Skill to this repo?
Yes — PRs welcome. We want Skills for:
- E-commerce SEO (Shopify-specific)
- Local SEO + Google Business Profile workflows
- Programmatic SEO at scale
- AI search rank tracking
- Anything else SEO/AEO that benefits from an agent playbook

---

## License

MIT. See [LICENSE](LICENSE).

Built by [Ranki.io](https://ranki.io). Companion to [`1fancy/seo-aeo-audit-mcp-ranki`](https://github.com/1fancy/seo-aeo-audit-mcp-ranki).
