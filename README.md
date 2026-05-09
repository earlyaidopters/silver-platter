# /silver-platter

A Claude Code skill that interviews any business owner about their day-to-day tools, maps their back of house into a visual data architecture, and generates a plain-English 30-day build plan for layering AI on top.

The whole thing rests on one idea: **80% of building an Agentic OS is data prep. The 20% AI layer is easy.** This skill is the 80%.

![Pantry → Prep → Plate → Then Agents](https://img.shields.io/badge/Pantry_%E2%86%92_Prep_%E2%86%92_Plate-Then_Agents-E07A4F?style=for-the-badge)

---

## What it does

You run `/silver-platter` inside any Claude Code project. The skill:

1. **Silently audits** your project folder for existing Claude Code setup (`.claude/`, `silver_platters/`, `data/`, hooks, agents, rules). If you've already started building, it skips questions about what's there.

2. **Interviews you** in plain English about your tools and workflow. No jargon. It asks what you use, how big your audience/customer base is, and what weekly task you'd most want to automate. It never asks you about "format", "cadence", "volume", or anything that sounds like a database column.

3. **Builds a data map** using the Pantry / Prep / Plate metaphor:
   - **Pantry** = every place your raw data already lives (Shopify, your inbox, your EHR, your POS)
   - **Prep Table** = weekly summary files ("silver platters") that Claude will write for you
   - **Plate** = the briefs and drafts that land in front of you Monday morning

4. **Generates recipes** showing how your tools combine for specific business outcomes. Each recipe has: a headline you can screenshot, a "today, manually" movie scene of how you do it by hand, a "next Monday with this live" description, and a time-saved estimate.

5. **Generates a 30-day build plan** with 5 prioritized steps (conversion hook, silver platters, orchestrator + specialists, audit log + approval gates, slash commands). Each step has "you'll know it's working when" so you can verify yourself.

6. **Maps your interaction layer** showing WHERE you'll actually read these briefs (Slack, iPad, email, Telegram) and suggests channels you might be missing.

7. **Renders a self-contained HTML file** (`data_map.html`) with 4 interactive tabs you can open in any browser. No external dependencies. Email it, share it, screenshot it.

8. **Generates OPPORTUNITIES.md** listing everything to build, grouped by Claude Code primitive (skills, subagents, hooks, rules, CLIs), with a plain-English glossary so every term is translated.

9. **Generates a handoff prompt** you can copy-paste to `@claude-code-guide` if you want the actual `.claude/` folder scaffolded for you.

---

## The HTML output

The `data_map.html` file has 4 tabs:

### Tab 1: What you have today
Cards for every tool in your Pantry, every silver platter in your Prep Table, every brief in your Plate. Click any card for plain-English details including how to connect it to Claude (MCP, CLI, or API with pros/cons for each).

### Tab 2: Data flow
A Sankey-style diagram showing how data moves left to right: raw sources feed silver platters, platters feed briefs, briefs land in the channels you actually use (Slack, iPad, email). If a Pantry box has no line leaving it, that data is sitting unused.

### Tab 3: What we'll build (recipes)
Recipe cards with outcome-first headlines and coral time-saved badges. Click any recipe for the full walkthrough: who triggers it, what Claude does step by step, what the before/after looks like. Each recipe also has a "today, manually" section describing how you do this by hand right now.

### Tab 4: Your 30-day build plan
Step-by-step setup with: what to do, why this step first, install commands, before/after comparison, and a "you'll know it's working when" sanity check. Step 0 (install Claude Code) is auto-rendered. For regulated archetypes (healthcare, law, wealth advisory), a Bedrock/BAA step is inserted at position 1.

---

## Install

```bash
cp -r silver-platter ~/.claude/skills/
```

Then in any Claude Code project:

```
/silver-platter
```

### Prerequisites

- [Claude Code](https://claude.ai/claude-code) installed
- Python 3.9+ with `jinja2` (`pip install jinja2`)
- That's it

---

## How the interview works

### Stage 0: Silent audit
Runs `scripts/audit_existing_folder.py` against your project. Detects `.claude/CLAUDE.md`, `settings.json`, skills, agents, rules, `data/`, `silver_platters/`, `outputs/audit_log.md`. If anything exists, the interview skips questions about it.

### Stage 1: Speed toggle
Walkthrough (~30 min, explains every choice) or Fast track (~10 min, assumes you know your tools).

### Stage 2: Business archetype
Describe your business in 1-2 sentences. The skill matches you to one of 9 archetypes:

| Archetype | Examples |
|---|---|
| `ecommerce` | Shopify store, DTC brand, live-stream commerce |
| `saas` | B2B/B2C subscription software |
| `professional_services` | Law firm, accounting practice, consultancy |
| `healthcare_clinic` | Medical practice, dental, specialty clinic |
| `wealth_advisory` | RIA, hedge fund, family office |
| `content_creator` | Newsletter, YouTube, podcast, course operator |
| `restaurant_multilocation` | Multi-unit food service |
| `real_estate_brokerage` | Agent team, indie brokerage |
| `local_trades` | HVAC, plumbing, landscaping, cleaning |

If none fit, it falls back to a free-text interview.

### Stage 3: Pantry tools
The skill asks outcome-shaped questions from `references/question_library.md`, customized per archetype. It never asks you about file formats, export cadences, or payload sizes. Those are derived silently from `references/tool_defaults.md`.

### Stage 4: Existing automation
Are you using Claude Code already? Any Zapier flows, scheduled jobs?

### Stage 5: Data-engineering reality check
For high-volume sources, the skill fires plain-English tips: "Six months of Shopify orders is tens of thousands of rows. We'll plan a weekly summary so the agent reads 26 rows instead of 60,000."

### Stage 6: Assemble the data map
Builds the JSON skeleton with auto-derived fields for every pantry/prep/plate item (display names, volume-friendly labels, sample content previews, governing rules).

### Stage 6.5: Recipes
Walks you through 4-7 starter recipes from `references/recipe_templates.md` for your archetype. Confirms each one with you. Adds your actual tool names and artifact names. You can add your own.

### Stage 6.6: Setup priority
Builds the 5-step build plan from `references/setup_priority_template.md`. Customizes each step with your tools, silver platter names, and bot names.

### Stage 6.7: Interaction layer
Asks where you want to read your briefs (Slack, iPad, email, terminal). Suggests 1-2 channels you might be missing (e.g., Telegram for time-sensitive alerts).

### Stage 7: Render the HTML
Runs `scripts/render_data_map.py` to produce the self-contained `data_map.html`.

### Stage 8: Generate OPPORTUNITIES.md
Groups everything to build by Claude Code primitive, with a glossary footer translating every term (skill = a reusable mini-tool, subagent = a specialist staffer, hook = an auto-trigger, rule = guardrails).

### Stage 9: Handoff prompt
Renders a copy-paste prompt for `@claude-code-guide` to scaffold the `.claude/` folder.

### Stage 9.5: "I don't have a developer" branch
Asks explicitly: are you comfortable in Terminal, or would you rather hand this off? If you don't have a developer, the skill generates `hire_a_builder.md` with an Upwork job posting template, budget ranges ($300-3,000), and the 5 skills a freelancer needs.

### Stage 10: Confirmation
Shows you the 4 output files. Offers to teach any piece in plain English or hand off to `@claude-code-guide`.

---

## Output

After a successful run, your project has:

```
silver_platter_output/
├── data_map.json                    # canonical data map (full schema)
├── data_map.html                    # self-contained 4-tab visualization
├── OPPORTUNITIES.md                 # plain-English priority list with glossary
└── claude_code_guide_handoff.txt    # copy-paste prompt for @claude-code-guide
```

---

## The 4-layer Agentic OS

Every data map this skill produces follows a 4-layer architecture:

```
Layer 4 — Automation (hooks)
  SessionStart: converts messy files on session start
  PostToolUse: logs every action to audit_log.md
  Stop: blocks exit if a draft is unsigned

Layer 3 — Workers (subagents)
  Chief-of-staff orchestrator routes to specialists
  Each specialist scoped to one silver platter

Layer 2 — Knowledge (skills + MCPs)
  Project-level skills specific to your business
  Global skills for tools you'll always have
  MCP servers for real-time tool access

Layer 1 — Identity (CLAUDE.md + path-scoped rules)
  The foundation. Who your agents are.
  What they're allowed to read.
  Nested per-folder for domain isolation.
```

80% of the work is Layers 1-2 (data prep). 20% is Layers 3-4 (the AI on top). Most people build Layer 4 first and wonder why nothing works.

---

## What's in this repo

```
silver-platter/
├── SKILL.md                         # the skill entry point Claude Code reads
├── references/
│   ├── archetypes.md                # 9 business archetypes with pain patterns
│   ├── question_library.md          # per-archetype interview questions
│   ├── tool_defaults.md             # per-tool schema defaults (format, cadence, volume)
│   ├── recipe_templates.md          # 6-10 starter recipes per archetype with full schemas
│   ├── setup_priority_template.md   # universal 5-step build plan + regulated exceptions
│   ├── cli_inventory.md             # CLI/skill catalog per service domain
│   ├── opportunity_patterns.md      # data shape → recommended Claude Code primitive
│   ├── data_engineering_tips.md     # plain-English tips triggered by volume/format
│   ├── claude_code_handoff_template.md  # Jinja2 template for the handoff prompt
│   └── greenfield_vs_audit_detection.md # how Stage 0 detection works
├── scripts/
│   ├── render_data_map.py           # data_map.json → data_map.html via Jinja2
│   ├── audit_existing_folder.py     # read-only audit of cwd for existing setup
│   └── templates/
│       └── data_map.html.j2         # the generic HTML template (Pantry/Prep/Plate/Sankey)
└── examples/                        # pre-rendered data maps for 5 operators
    ├── marco_ecommerce/             # Shopify + Meta + TikTok live-stream e-commerce
    ├── sally_law/                   # 12-attorney boutique law firm, Bedrock + matter walling
    ├── devon_saas/                  # B2B SaaS, 280 customers, Codex worktree pattern
    ├── dr_mehra_clinic/             # healthcare clinic, PHI scoping + controlled bridges
    └── dr_anwar_derma/              # solo dermatology practice, Bedrock + biopsy triage
```

---

## Supported archetypes

Each archetype has its own question chain, recipe starter library, orchestrator/specialist table, and silver-platter defaults.

| Archetype | Starter recipes | Key Claude Code pattern taught |
|---|---|---|
| E-commerce | 7 (P&L brief, stream prep, refund triage, customer voice, abandoned cart, spend pacing, inventory restock) | Silver-platter pattern, summary tables |
| SaaS | 6 (Monday triage, Codex worktree, weekly product, churn investigation, Slack-to-Linear, release changelog) | Orchestrator + specialists, worktree |
| Professional services | 6 (new matter, time entry, MSJ focus, post-mortem, engagement letter, conflict check) | Path-scoped rules, matter walling |
| Healthcare clinic | 5 (morning brief, lab triage, appeal draft, weekly P&L, referral letter) | Bedrock + PHI scoping, controlled bridges |
| Wealth advisory | 8 (quarterly letter, trade committee, risk check, personal-trade pre-clear, Reg BI doc, client review, billing recon, marketing review) | Bedrock + personal-trade scoping |
| Content creator | 10 (newsletter idea engine, YouTube-to-newsletter, subscriber retention, Gumroad launch, community substitute, creator finance, and more) | Back-catalog as corpus, no-community path |
| Restaurant multi-location | 9 (daily close, food cost, staff schedule, labor variance, aggregator recon, review response, weekly P&L, food waste, manager standup) | Per-location scoping |
| Real estate brokerage | 8 (listing packet, buyer match, closing checklist, lead triage, lead-source ROI, agent scoreboard, dead-deal postmortem, followup rescue) | Per-agent scoping |
| Local trades | 8 (daily dispatch, quote followup, seasonal prep, review response, voicemail triage, weekly revenue, parts restock, maintenance plan) | No-developer path, honest iMessage handling |

---

## For non-technical operators

The skill explicitly handles operators who have never opened Terminal:

- Questions are phrased in plain English with jargon translated inline on first use
- "I don't have one" is always a valid answer to any "where does X live?" question
- The 30-day build plan includes setup time estimates and "you'll know it's working when" checks
- Stage 9.5 asks whether you're comfortable in Terminal or need to hand off
- If you don't have a developer, the skill generates a hire-a-builder template with Upwork posting, budget guidance ($300-3,000), and the skills to look for

---

## For regulated businesses

Healthcare clinics, law firms, and wealth advisors get additional handling:

- **Bedrock step inserted at position 1** in the build plan (before any data touches the model)
- **PHI scoping rule** for healthcare (path-scoped, always-on for `data/clinical/**`)
- **Matter walling** for law firms (per-matter CLAUDE.md, no cross-matter reads)
- **Personal-trade scoping** for wealth advisors (client data + pre-clear attestations isolated)
- **Controlled cross-domain bridge** for the one agent allowed to read both clinical and billing (e.g., insurance appeal drafting), logged on every read

---

## Multi-tenant businesses

Restaurants with multiple locations, brokerages with multiple agents, clinics with multiple providers:

- The skill detects team size from interview answers
- Per-tenant scoping pattern is surfaced in Stage 5
- Each tenant gets a path-scoped rule (`data/locations/{id}/**`, `data/agents/{id}/**`, `data/providers/{id}/**`)
- The orchestrator sees everything, specialists see only their slice

---

## How the HTML template works

`scripts/templates/data_map.html.j2` is a single-file Jinja2 template that produces a self-contained HTML page with:

- Inline CSS (no external stylesheets)
- Inline JavaScript (no frameworks, no dependencies)
- Three-lane Pantry/Prep/Plate card layout with click-to-modal details
- SVG Sankey diagram rendered dynamically from the data map JSON
- Recipe cards with time-saved badges and modal walkthroughs
- Setup steps with requires/working-when blocks
- Interaction-layer 4th column on the Sankey when `interaction_layer` is present
- Status badges (green check = have it, gray ? = missing, coral ! = opportunity)
- Sticky legend bar and tab navigation
- Hero stats strip (tools mapped, recipes ready, quick wins, hours/week back)
- Jargon glossary in modals (skill = mini-tool, subagent = specialist staffer, hook = auto-trigger, rule = guardrails)
- Connection method tabs renamed for operators: "Easiest setup" (MCP), "If you live in the terminal" (CLI), "Custom build" (API)

The template renders any persona dict matching the schema. The examples in `examples/` are test fixtures demonstrating it.

---

## Schema

The full `data_map.json` schema is documented in SKILL.md Stage 6. Key fields:

```
business.name, archetype, stack_summary, headline, lead, hours_back_per_week

pantry[].id, tool, format, cadence, volume, feeds, status, cli_skill,
         explanation, connection_methods[], volume_friendly, paying_unused

prep[].id, name, domain, sources[], schedule, status, display_name,
      domain_friendly, sample_content, governing_rule_excerpt

plate[].id, name, agent, consumers[], approval_gate, reads_from[], status,
       agent_friendly, approval_friendly, sample_output, ideation_loop[]

recipes[].id, name, headline, time_saved_per_week, manual_today,
          monday_difference, goal, ingredients[], ingredients_friendly[],
          claude_code_stack{skills, subagents, hooks, rules},
          walkthrough[{actor, action}], before_claude_code, after_claude_code

setup_priority[].step, title, title_friendly, requires, what_to_do, why,
                install, before, after, working_when, setup_time

interaction_layer[].id, channel, type, status, description, consumes[]

opportunities[].surface, type, title, explanation, claude_code_feature,
               feature_name, estimated_impact
```

---

## Contributing

This skill is open-source and free. If you run it against your own business and find a gap (missing archetype, missing tool in defaults, question that confuses non-technical operators), open an issue or PR.

The most valuable contributions are:
- New archetype question chains in `references/question_library.md`
- New tool entries in `references/tool_defaults.md`
- New recipe starters in `references/recipe_templates.md`
- Bug reports from real operators who got confused during the interview

---

## License

MIT. Use it, fork it, sell services on top of it. Attribution appreciated but not required.
