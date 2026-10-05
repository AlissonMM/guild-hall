# Guild Hall

🇬🇧 **English** · [🇧🇷 Português](README.pt-BR.md)

Claude Code plugin · v0.2.2

Nine Claude agents and skills organized as RPG classes. Each class has a role in the party, and they pass information to each other through files inside your project, so nobody asks you the same thing twice.

Visual version of this page: open [`index.html`](index.html) in your browser.

> The agents and skills themselves are written in Portuguese, but they work in any language: they detect your project's language and answer in the language you use.

## Recruit the guild

```
/plugin marketplace add AlissonMM/guild-hall
/plugin install guild-hall@alissonmm-toolkit
```

Run these inside `claude` in the terminal, or use **+ › Plugins** in the desktop app. Then open a new conversation and start with `/guildmaster`.

To update after a change in the repository: `claude plugin update guild-hall@alissonmm-toolkit` (or **Update now** in the plugins panel), then open a new conversation.

## The classes

Skills run in the main chat and can talk to you. Agents work in isolation and return only the result.

| Class | Type | Role | Tincture |
|---|---|---|---|
| Guildmaster | Skill | organizes | Or (gold) |
| Loremaster | Agent | knows the world | Azure (blue) |
| Ranger | Agent | explores | Vert (green) |
| Tactician | Skill | plans | Purpure (purple) |
| Blacksmith | Skill | forges the back end | Sable (black) |
| Enchanter | Skill | shapes the front end | Murrey (mulberry) |
| Summoner | Skill | summons the project | Tenné (orange) |
| Seer | Agent | sees the result | Celeste (sky blue) |
| Inquisitor | Agent | inspects | Gules (red) |

### Guildmaster · Skill · organizes
Explains how the party works: what each class does, in which order to call them and which files one hands to another.
- **Call:** `/guildmaster`
- **Writes:** nothing

### Loremaster · Agent · knows the world
Reads the project without opinions and keeps the reference document at any stage: what is planned, in progress and implemented, business rules with evidence (file:line), stack, deployment and known issues. Every run, it reports what changed.
- **Call:** "use the loremaster to document this project"
- **Writes:** `docs/PROJETO.md`

### Ranger · Agent · explores
Researches flows and screens of real apps on Mobbin, Lazyweb, Refero or the web and saves the references, also to Figma if you want. It pauses to ask questions and is resumed by the main chat.
- **Call:** "use the ranger to research the checkout flow"
- **Writes:** `docs/pesquisa-fluxos/`

### Tactician · Skill · plans
Mode A: stack, architecture and back-end and front-end decisions, always recommending an option. Mode B: a design system with a reference page published in the product's own style.
- **Call:** `/tactician`
- **Writes:** `docs/adr/`, `docs/design-system.md`

### Blacksmith · Skill · forges the back end
Rules for writing back-end code: security (OWASP), readability, performance, migrations and tests. It only asks about decisions that are expensive to undo.
- **Call:** loads on its own, or `/blacksmith`
- **Writes:** code and new ADRs

### Enchanter · Skill · shapes the front end
Rules for writing front-end code: design system tokens only, WCAG 2.2 AA accessibility, mobile-first, screen states, performance and XSS protection.
- **Call:** loads on its own, or `/enchanter`
- **Writes:** code and new ADRs

### Summoner · Skill · summons the project
Checks your machine, recommends Docker Compose, hybrid or native, creates what is missing, starts everything and generates a startup skill for the project. It also prepares the deployment.
- **Call:** `/summoner` · "start the project"
- **Writes:** `docker-compose.yml`, `.claude/skills/startup-*`, `docs/deploy.md`

### Seer · Agent · sees the result
Opens the app in the browser, walks through the flow at 375px and 1280px and compares it with the design system and the references. Reports console, network and accessibility errors.
- **Call:** "use the seer on the login flow at http://localhost:4200"
- **Writes:** only the report

### Inquisitor · Agent · inspects
Reviews finished back-end and front-end code: security, accessibility, performance, ADRs, design system and tests. Returns findings by severity and a verdict.
- **Call:** "use the inquisitor on the changes"
- **Writes:** only the report

## The campaign

The recommended order for a new project. Each step leaves a file that the next one reads.

1. **Tactician · Mode A**: defines the back-end and front-end stack and architecture with you and records each decision in `docs/adr/`.
2. **Ranger**: researches how well-known apps solve your product's main flows.
3. **Tactician · Mode B**: creates the design system from the researched flows and publishes the reference page.
4. **Blacksmith and Enchanter**: you and the main chat build the back end and the screens following the ADRs and the design system.
5. **Summoner**: starts the project on your machine and generates the startup skill for next time.
6. **Seer, Inquisitor and Loremaster**: after each feature, browser check, code review and documentation update.
7. **Summoner · deployment**: when you are ready to publish, it picks the target, sets up HTTPS, secrets and backups, and deploys with your confirmation.
8. **Loremaster**: before publishing, reviews the whole document and points out where the plan and the code diverge. It can also be called right at the start to document the plan.

### An existing project you don't know yet
1. **Loremaster** documents the project.
2. **Tactician A** records what already exists as ADRs.
3. **Tactician B** extracts the current style, if there is no design system.
4. **Summoner** learns how to start the project.
5. Continue as in the campaign, from step 4.

### A new feature with a screen
1. **Ranger** brings the references.
2. You decide what to adopt.
3. **Blacksmith** and **Enchanter** build it.
4. **Seer** and **Inquisitor** check it.
5. **Loremaster** updates the documentation.

## The scrolls

The files the classes leave in your project. They are the party's shared memory.

| File | Written by | Read by |
|---|---|---|
| `docs/PROJETO.md` | Loremaster | every other class |
| `docs/pesquisa-fluxos/<topic>.md` | Ranger | Tactician, Enchanter, Seer, Blacksmith |
| `docs/adr/*.md` | Tactician, Blacksmith, Enchanter | Blacksmith, Enchanter, Seer, Inquisitor, Loremaster |
| `docs/design-system.md` + page | Tactician | Enchanter, Seer, Inquisitor |
| `docker-compose.yml`, `.claude/skills/startup-*`, `docs/deploy.md` | Summoner | Seer (URLs of the running app), Loremaster |

## Guild rules

Agreements the main chat follows when calling the agents.

- **The ranger pauses.** When it replies `STATUS: AGUARDANDO_RESPOSTA`, its questions go to you exactly as they came, and the same agent is resumed with your answers.
- **The loremaster is impartial.** The briefing carries only scope, format and output location. No summary or opinion about the system. It can be called at any stage of the project.
- **The seer needs the app running.** It receives the URL, the flow and the test data. If the app is not running, call the project's startup skill or the summoner first.
- **The inquisitor has a scope.** Uncommitted changes, a branch or folders. Critical and High findings reach you before moving on.

## Repository layout

```
.claude-plugin/
  plugin.json        # plugin manifest
  marketplace.json   # lets this repo be installed as a marketplace
agents/              # agents (one .md per agent)
skills/              # skills (one folder per skill, with SKILL.md)
index.html           # visual version of this README
README.md            # this version, in English
README.pt-BR.md      # Portuguese version
```

## Principles

- **Skill** = reusable knowledge and procedure (runs in the context of whoever loads it).
- **Agent** = heavy, noisy or parallel work, with isolated context.
- **Main chat** = decisions with the user and tightly coupled work.
- Handoffs between stages happen through **files** inside the project.
- No class carries project-specific context: they all read the current project and detect the stack on the fly.
- Every new class goes into the Guildmaster, both READMEs, `index.html` and the repository's "About".

---

Guild Hall · github.com/AlissonMM/guild-hall
