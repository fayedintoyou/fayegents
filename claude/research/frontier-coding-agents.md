# How leading firms run coding agents

*Research summary, 2 November 2026. Compiled with Claude from public sources. Most figures are self-reported by the companies and unaudited; a few come from press coverage of talks.*

## Bottom line

The frontier has moved past spec frameworks and orchestration layers to background agents in well-equipped sandboxes, gated by CI and human review.

- **Agents already write most of the code at the leaders.** Ramp credits its agent with 74% of merged PRs, Stripe merges 1,300+ agent-written PRs a week, and Anthropic says Claude authors 80%+ of its merged code.
- **The winning investment is the environment, not orchestration.** That means the same dev environment engineers use, checks that run in seconds, and review at merge.
- **Multi-agent pipelines are a niche tool.** Even Cognition and Cursor, the most pro-swarm vendors, say one agent should write while others research or review.
- **Spec frameworks lack evidence.** Every leader writes short plans and uses tests as the definition of done, but none of the firms profiled here runs a spec-driven development (SDD) framework. Studies show added review time and no defect benefit.
- **Banks are on assistants and isolation.** They have rolled out coding tools to tens of thousands of developers. Their stated controls are sandboxes, spend caps and human review. Regulators ask about permissions, audit trails, oversight and shutdown, not per-step gates.

## The common pattern

The leaders converge on one shape: one agent per task, running in the same environment engineers use, checking its own work, with a human approving at merge.

1. **Start from where work already lives.** Agents are kicked off from Slack, tickets or a web page (Stripe, Ramp, Spotify, Shopify, Linear).
2. **Run in a real dev environment.** Pre-warmed sandboxes hold the full stack and are cut off from production. Stripe reuses its engineers' devboxes, and Uber's agent platform grew out of its remote dev environments.
3. **One agent writes and checks itself.** It edits, runs lint and tests, and fixes failures in a loop. Fixed steps (lint, push, CI) run around it, with caps such as Stripe's two CI rounds.
4. **A human approves at merge.** PRs are credited to the requester and reviewed by someone else. Only low-risk fleet changes auto-merge (Spotify).

Five principles sit behind that shape:

- **Simple beats orchestrated.** Anthropic advises the [simplest solution that works](https://www.anthropic.com/research/building-effective-agents). Cognition now says multi-agent setups work only when one agent writes and the others research or review. Cursor credits many gains to [removing complexity](https://cursor.com/blog/scaling-agents). Parallelism comes from many independent sessions, one per task, not from agents handing work to each other.
- **Harnesses shrink as models improve.** Anthropic removes harness parts as models get better, Spotify reportedly dropped its LLM judge, and specs help weak models far more than strong ones.
- **Context is short and lives in the repo.** AGENTS.md acts as a map, rules are scoped by directory (Stripe), and Cloudflare generated AGENTS.md files for about 3,900 repos.
- **Review is the bottleneck.** OpenAI names human attention, Ramp names judgment, and Spotify has 76% more PRs to review. Investment is shifting to verification: AI reviewers (Uber, Cloudflare, Cursor), hidden verifiers (Spotify) and real-browser checks (Ramp).
- **Pipelines are for fleets.** State-machine pipelines appear where work is mechanical and checkable, such as Airbnb's test migration and Spotify's Fleet Management. The big multi-agent "swarm" wins were new projects with near-perfect test suites, not existing codebases.

## Firm by firm

### Product and fintech companies

| Company | What they built | How work is gated | Reported scale |
| --- | --- | --- | --- |
| Ramp | [Inspect](https://ramp.com/blog/introducing-inspect): OpenCode in [Modal sandboxes](https://modal.com/blog/how-ramp-built-a-full-context-background-coding-agent-on-modal) holding the full stack, with tools to screenshot, test and read telemetry; started from Slack, GitHub, Linear, web or mobile | PRs are credited to the requester, so a second person must approve | 74% of merged PRs (Oct 2026); opened to outside companies the same month |
| Stripe | [Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents): one-shot agents on a fork of Block's goose, running on the same pre-warmed devboxes humans use | Code-defined [blueprints](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) run fixed steps (lint, push) around agent steps; two CI rounds max, then a human reviews | 1,300+ merged PRs a week with no human-written code (Feb 2026) |
| Spotify | [Honk](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1): Claude Agent SDK in Spotify's own harness, plugged into its Fleet Management migration system | Verifiers behind one tool, plus a [stop hook](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) that blocks the PR until they pass; safe fleet changes auto-merge | About 1,000 merged PRs every 10 days (Mar 2026); a backend Java migration in [three days](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) |
| Shopify | [River](https://shopify.engineering/under-the-river): a Slack agent limited to public channels, on a shared internal agent platform; [Roast](https://shopify.engineering/introducing-roast) for scripted workflows | Human review still required for production code | Coauthors 1 in 8 merged PRs (May 2026) |
| Uber | [uReview](https://www.uber.com/blog/ureview/), an AI code reviewer, plus Minion, a background-agent platform grown from its remote dev environments | Engineers review and approve every agent change | uReview covers 90%+ of ~65,000 weekly diffs; agents opened [11% of merged PRs](https://background-agents.com/summit/sessions/nikhil-ramakrishnan/) (May 2026) |
| Airbnb | [Test migration pipeline](https://medium.com/airbnb-engineering/accelerating-large-scale-test-migration-with-llms-9565c208023b): each file moved through refactor, Jest, lint and TypeScript steps, with retries | A file advanced only when its checks passed | About 3,500 files in 6 weeks vs. an estimated 1.5 years (Mar 2025); [60% of new code AI-written](https://techcrunch.com/2026/05/08/airbnb-says-ai-now-writes-60-of-its-new-code/) (May 2026) |
| Coinbase | Cursor and Copilot mandated in 2025; background agents now work tickets | Core cryptography code is still reviewed line by line | An executive claims 95–100% of code is written by or with LLMs ([Jul 2026](https://cointelegraph.com/news/over-95-of-coinbases-code-is-now-written-with-ai)) |

### AI labs and dev-tool companies

| Company | How they build | How work is gated | Reported scale |
| --- | --- | --- | --- |
| OpenAI | [Harness engineering](https://openai.com/index/harness-engineering/): a team of 3, later 7, built about 1M lines with no hand-written code. AGENTS.md is a ~100-line map; plans live in the repo. [Symphony](https://openai.com/index/open-source-codex-orchestration-symphony) runs one Codex agent per ticket | Custom linters and structural tests whose errors tell the agent how to fix them; cleanup agents fight drift; humans review | About 1,500 PRs at 3.5 per engineer per day (Feb 2026); Symphony lifted landed PRs about 500% for some teams |
| Anthropic | Claude Code across engineering: parallel sessions, plan first, and always give Claude a check to run ([best practices](https://code.claude.com/docs/en/best-practices)) | Tests as the check; a separate skeptical evaluator beats self-evaluation ([harness design](https://www.anthropic.com/engineering/harness-design-long-running-apps)) | 80%+ of merged code authored by Claude ([May 2026](https://www.anthropic.com/institute/recursive-self-improvement)) |
| Cloudflare | [Internal stack](https://blog.cloudflare.com/internal-ai-engineering-stack/) built mostly on OpenCode, with AGENTS.md generated for about 3,900 repos. [Code Mode](https://blog.cloudflare.com/code-mode/) has models write code against typed APIs instead of calling tools | [AI code review](https://blog.cloudflare.com/ai-code-review/): a coordinator plus up to 7 specialist reviewers at about $1 a review; humans still review | Weekly merge requests up from about 5,600 to 8,700+ (Apr 2026) |
| Linear | [Linear Agent](https://linear.app/now/how-we-built-linear-agent): one constrained agent with skills and approval checks; agents take issues as delegates | Humans stay accountable; its [guidelines](https://linear.app/developers/aig) state that an agent cannot be held accountable | Almost 700 agent PRs merged internally in a month (Jun 2026) |
| Cursor | Cloud agents in VMs; [Bugbot](https://cursor.com/blog/building-bugbot) reviews all internal code | Bugbot's fix rate rose from 52% to 70%+ after moving to a single agent loop | Agents created 35% of internally merged PRs ([Feb 2026](https://cursor.com/blog/third-era)) |
| GitHub | [Agent HQ](https://github.blog/news-insights/company-news/welcome-home-agents/) runs Copilot, Claude and Codex agents side by side | The Copilot CLI team cut back delegation to subagents and saw 23% fewer tool failures ([Jun 2026](https://github.blog/ai-and-ml/how-we-made-github-copilot-cli-more-selective-about-delegation/)) | Octoverse reports 40%+ of PRs agent-started by Sep 2026, up from 17% in March ([Octoverse](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)) |
| Cognition | Devin. ["Don't Build Multi-Agents"](https://cognition.com/blog/dont-build-multi-agents) (2025), revised in ["Multi-Agents: What's Actually Working"](https://cognition.com/blog/multi-agents-working) (Apr 2026) | Extra agents may research or review, but only one agent writes code | 659 Devin PRs merged internally in one week (Feb 2026) |

These figures aren't directly comparable. "AI-written code" and "PRs written entirely by an agent" measure different things, and no company publishes revert or incident rates for agent PRs.

## Regulated peers

Big banks have moved AI coding tools from pilots to tens of thousands of developers. The controls they describe publicly are isolation, spend caps and human review, not orchestration frameworks.

| Bank | Scale | Claimed result | Agents and controls |
| --- | --- | --- | --- |
| JPMorgan Chase | 65,000+ technologists ([American Banker](https://www.americanbanker.com/news/2026-the-most-powerful-women-in-banking-5-lori-beer-jpmorganchase)); about 8,000 Claude licences (Aug 2026) | Coding phase 10–20% faster; the CIO says most code is now assisted or written agentically (2026) | "Devspace" isolates AI tools from employee credentials and internal systems; $2,000/month Claude Code cap for some engineers ([Sep 2026](https://www.newsquawk.com/headlines/jpmorgan-jpm-introduced-usd-2k-monthly-claude-code-spending-limits-for-some-engineers-and-is-rolling-out-a-more-secure-devspace-environment-business-insider-reports)) |
| Goldman Sachs | About 12,000 engineers ([Fortune](https://fortune.com/2026/05/08/goldman-sachs-cio-marco-argenti-tech-ai-future-of-work-employees/)) | Up to 20% from its developer copilot (May 2025) | Devin pilot with human supervision ([Jul 2025](https://www.nbcchicago.com/news/business/money-report/goldman-sachs-is-piloting-its-first-autonomous-coder-in-major-ai-milestone-for-wall-street/3787263/)); 2026 agents built with Anthropic target operations, not coding |
| Citi | 30,000 developers with AI coding tools ([Jan 2025](https://finance.yahoo.com/news/citi-deploys-ai-coding-tools-152606091.html)) | AI-assisted code review frees about 100,000 hours a week ([Investor Day, May 2026](https://www.citigroup.com/rcs/citigpa/storage/public/investor-day/citi-ID26-ceo-presentation-transcript.pdf)) | Scaling agents firm-wide after a 5,000-user pilot (Sep 2025) |
| Bank of America | 19,000 developers ([Jul 2026](https://www.americanbanker.com/news/bank-of-americas-moynihan-touts-good-economics-of-ai)) | More than 20% developer productivity | None disclosed |
| Morgan Stanley | 15,000 developers with access to its in-house DevGen.AI (Jun 2025) | 9M lines of legacy code reviewed, about 280,000 hours saved (2025) | The AI writes plain-English specs of old code; developers write the new code ([Business Insider](https://www.aol.com/team-engineers-saved-morgan-stanley-094301348.html)) |
| Wells Fargo | Not disclosed | Engineers 30–35% more efficient at writing code ([Dec 2025](https://www.retailbankerinternational.com/news/wells-fargo-job-cuts-ai-rollout/)) | None disclosed |
| Capital One | About 14,000 technologists on one delivery pipeline ([Jan 2026](https://devinterrupted.buzzsprout.com/1422892/episodes/18459299-how-capital-one-supports-14-000-technologists-with-one-pipeline-ameesh-paleja)) | No coding figures disclosed | Staged pilots, risk tiers by consequence, humans in the loop for high-risk decisions ([Euromoney](https://www.euromoney.com/article/2eh2s01l11023kmxtleyo/fintech/prem-natarajan-on-capital-ones-ai-stairway-to-heaven/)) |

No bank publishes how it measures productivity, so the percentages aren't comparable.

**What regulators actually require.** In April 2026, [SR 26-2](https://www.federalreserve.gov/supervisionreg/srletters/sr2602.htm) and [OCC Bulletin 2026-13](https://www.occ.gov/news-issuances/bulletins/2026/bulletin-2026-13.html) replaced SR 11-7 and put generative and agentic AI outside model risk guidance, pending a request for information. In October, the Fed, OCC and FDIC began informally asking banks about agent permissions, audit trails, human oversight and how they would shut agents down ([Reuters](https://kfgo.com/2026/10/13/exclusive-banking-watchdogs-ramp-up-ai-scrutiny-as-stability-fears-mount-sources-say/)). No published guidance asks for controls between agent steps, so AI-written code still runs through ordinary change management: review, testing and separation of duties.

## Where spec-driven development fits

Writing down intent before an agent codes is mainstream; heavyweight SDD frameworks are not. The measurements so far show they add review time without catching more bugs, and the frameworks themselves are getting lighter.

| Tool | Approach | Status, Nov 2026 |
| --- | --- | --- |
| [GitHub Spec Kit](https://github.com/github/spec-kit) | Spec, plan and task templates that work across many agents | v1.0 in Aug 2026; about 143k GitHub stars; Thoughtworks rates it "Assess" |
| [Kiro](https://kiro.dev/blog/one-year/) (AWS) | IDE that writes requirements, design and task specs | 250k developers in its first three months; added a quick mode with no approval gates; 2026 focus moved to platform features |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) | Per-change proposals and spec deltas | v1.0 (Jan 2026) replaced its fixed sequence with flexible actions; about 73k stars; "Assess" |
| [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) | Persona-based agile workflow (analyst, PM, architect, developer) | v7 still in alpha; core skills cut from 14 to 8; Thoughtworks calls it more rigid than OpenSpec |
| [Tessl](https://tessl.io/) | Specs as the source of truth, with a spec registry | The spec framework no longer appears on its site, which now leads with agent skills |

**What the evidence says**

- **Review cost.** In one test, a Spec Kit feature took about 3.5 hours of review and 2,577 lines of Markdown, versus about 24 minutes with plain prompting ([Scott Logic](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)). Another produced 1,300 lines of text for a small date-display feature ([Marmelab](https://marmelab.com/blog/2025/11/12/spec-driven-development-waterfall-strikes-back.html)).
- **Bug catching.** A spec baseline did not help reviewers find more bugs (recall 0.525 vs. 0.518), and review took 48 vs. 27 minutes. Specs lifted a weak model by about 21 points and a strong one by about 2 ([InfoQ, Sep 2026](https://www.infoq.com/articles/when-spec-driven-development-pays-off/)).
- **Defects.** A study of 88,052 pull requests found spec artifacts did not reduce defects, though it studied linked tickets rather than SDD tool output ([ESEM 2026](https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ESEM.2026.84)).
- **Maintenance.** Of 470,795 spec files across 73,030 public repos, 81.5% have a single commit and 98.3% a single author. Specs get written once, not maintained ([SpecMine, Oct 2026](https://arxiv.org/abs/2608.25202)).
- **Company results.** Asana tried Spec Kit and OpenSpec, and neither stuck. Its own lightweight skill coincided with 38% more PRs a week among seven heavy users, uncontrolled ([Asana](https://asana.com/inside-asana/spec-driven-development)). No large company has published controlled results from any SDD framework.
- **Analyst view.** Thoughtworks rates Spec Kit and OpenSpec "Assess" but rates [AGENTS.md](https://www.thoughtworks.com/radar/techniques/agents-md) and [Agent Skills](https://www.thoughtworks.com/radar/techniques/agent-skills) "Trial", one ring closer to adoption.

**What leading teams do instead**

- **A short map, not a rulebook.** OpenAI's Codex team keeps AGENTS.md to about 100 lines, "a map, not a 1,000-page instruction manual," with design docs and plans checked into the repo ([OpenAI](https://openai.com/index/harness-engineering/)).
- **One living plan.** OpenAI's [ExecPlans](https://developers.openai.com/cookbook/articles/codex_exec_plans) are a single self-updating plan document per task. Anthropic's long-running agents track a JSON feature list with pass/fail flags plus a progress file ([Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)).
- **Tests as the definition of done.** Claude Code's guidance pairs a short CLAUDE.md and plan mode with failing tests the agent must make pass ([Claude Code docs](https://code.claude.com/docs/en/best-practices)). Spotify writes migration prompts as end states verified by tests.

The useful core of SDD, a short plan plus tests that define done, is something every leading team does. The ceremony around it is what the evidence does not support, and its benefit shrinks as models get stronger.

## Implications for regulated enterprises

Orchestration-heavy designs (a state machine coordinating separate agent sessions behind spec frameworks and rules-engine gates) put controls in the right places at the edges. In the middle they add orchestration the leaders have deliberately avoided.

| Design choice | What the leaders do | Verdict |
| --- | --- | --- |
| Deterministic steps around agents | Stripe's blueprints, Airbnb's per-file pipeline, Shopify's Roast and Spotify's verifiers all wrap one agent with fixed steps | Aligned, when it wraps one agent per task |
| Separate agent sessions per stage, passing structured outputs | One agent carries a task end to end, with the full context in its prompt | Diverges: each handoff drops context, and chained instructions drift |
| SDD frameworks as the front door | A short AGENTS.md, plans in the repo, and tests as the definition of done | Diverges: measured review cost with no measured defect benefit |
| Rules engines deciding pass, retry or human review | CI, linters and stop hooks inside the loop; human review at merge; Spotify reportedly dropped its LLM judge as models improved | Right at merge, deploy and data access; overhead between steps |
| Evidence at every step | PR history, CI logs and session transcripts; Shopify runs agents only in public Slack channels | Aligned in goal; regulators ask about permissions, audit trails, oversight and shutdown, not per-step artifacts |
| A central platform driven from a CLI | Background agents started from Slack, tickets or web, running in the same environments engineers use | Partly: a central platform is right, but a CLI-run pipeline keeps a human waiting on it |

**What to prioritize instead**

1. **An agent-ready sandbox.** Give agents the environment engineers already use, isolated from production and personal credentials, like Stripe's devboxes and JPMorgan's Devspace.
2. **Fast feedback inside the loop.** Lint and test results in seconds, a hook that blocks "done" until checks pass, and a cap on CI rounds.
3. **Gates at the boundaries.** Human review at merge with separation of duties (Ramp credits each agent PR to the requester), plus tight controls on deploys and data access.
4. **Short, repo-native context.** AGENTS.md as a map, skills loaded on demand, plans checked into the repo.
5. **Start where pipelines pay off.** Fleet-wide migrations and toil (Spotify, Airbnb), then background agents for feature work (Ramp, Stripe).
6. **Measure outcomes.** Track merged PRs, review time and revert rate, which no leader has yet published for agent PRs.

## Sources

**Product and fintech companies**

- [Stripe: Minions, part 1](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents) and [part 2](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) (Feb 2026)
- [Ramp: Introducing Inspect](https://ramp.com/blog/introducing-inspect) (Oct 2026); [Modal: how Ramp built its agent](https://modal.com/blog/how-ramp-built-a-full-context-background-coding-agent-on-modal) (Feb 2026)
- [Spotify: Honk part 1](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1) (Nov 2025), [part 3](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) (Dec 2025), [Coding is no longer the constraint](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) (Jun 2026); [InfoQ on the Honk rewrite](https://infoq.com/news/2026/03/spotify-honk-rewrite) (Mar 2026)
- [Shopify: Under the River](https://shopify.engineering/under-the-river) (May 2026); [Introducing Roast](https://shopify.engineering/introducing-roast) (Jun 2025)
- [Uber: uReview](https://www.uber.com/blog/ureview/) (Aug 2025); [Uber agent platform talk](https://background-agents.com/summit/sessions/nikhil-ramakrishnan/) (May 2026)
- [Airbnb: large-scale test migration with LLMs](https://medium.com/airbnb-engineering/accelerating-large-scale-test-migration-with-llms-9565c208023b) (Mar 2025); [TechCrunch on Airbnb's AI-written code](https://techcrunch.com/2026/05/08/airbnb-says-ai-now-writes-60-of-its-new-code/) (May 2026)
- [Cointelegraph on Coinbase](https://cointelegraph.com/news/over-95-of-coinbases-code-is-now-written-with-ai) (Jul 2026)

**AI labs and dev-tool companies**

- [OpenAI: Harness engineering](https://openai.com/index/harness-engineering/) (Feb 2026); [Symphony](https://openai.com/index/open-source-codex-orchestration-symphony) (Apr 2026); [ExecPlans](https://developers.openai.com/cookbook/articles/codex_exec_plans)
- [Anthropic: When AI builds itself](https://www.anthropic.com/institute/recursive-self-improvement) (May 2026); [Building effective agents](https://www.anthropic.com/research/building-effective-agents) (Dec 2024); [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) (Nov 2025); [Harness design for long-running apps](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Mar 2026); [Claude Code best practices](https://code.claude.com/docs/en/best-practices)
- [Cloudflare: internal AI engineering stack](https://blog.cloudflare.com/internal-ai-engineering-stack/) (Apr 2026); [AI code review](https://blog.cloudflare.com/ai-code-review/); [Code Mode](https://blog.cloudflare.com/code-mode/) (Sep 2025)
- [Linear: How we built Linear Agent](https://linear.app/now/how-we-built-linear-agent) (Aug 2026); [Agent interaction guidelines](https://linear.app/developers/aig)
- [Cursor: Scaling agents](https://cursor.com/blog/scaling-agents) (Jan 2026); [The third era](https://cursor.com/blog/third-era) (Feb 2026); [Building Bugbot](https://cursor.com/blog/building-bugbot)
- [GitHub: Agent HQ](https://github.blog/news-insights/company-news/welcome-home-agents/) (Oct 2025); [Copilot CLI delegation](https://github.blog/ai-and-ml/how-we-made-github-copilot-cli-more-selective-about-delegation/) (Jun 2026); [Octoverse 2026](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)
- [Cognition: Don't Build Multi-Agents](https://cognition.com/blog/dont-build-multi-agents) (Jun 2025); [Multi-Agents: What's Actually Working](https://cognition.com/blog/multi-agents-working) (Apr 2026)

**Regulated peers**

- [American Banker on JPMorgan's Lori Beer](https://www.americanbanker.com/news/2026-the-most-powerful-women-in-banking-5-lori-beer-jpmorganchase) (2026); [Newsquawk on JPMorgan's Devspace](https://www.newsquawk.com/headlines/jpmorgan-jpm-introduced-usd-2k-monthly-claude-code-spending-limits-for-some-engineers-and-is-rolling-out-a-more-secure-devspace-environment-business-insider-reports) (Sep 2026)
- [Fortune on Goldman's CIO](https://fortune.com/2026/05/08/goldman-sachs-cio-marco-argenti-tech-ai-future-of-work-employees/) (May 2026); [CNBC on Goldman's Devin pilot](https://www.nbcchicago.com/news/business/money-report/goldman-sachs-is-piloting-its-first-autonomous-coder-in-major-ai-milestone-for-wall-street/3787263/) (Jul 2025)
- [CIO Dive on Citi](https://finance.yahoo.com/news/citi-deploys-ai-coding-tools-152606091.html) (Jan 2025); [Citi Investor Day transcript](https://www.citigroup.com/rcs/citigpa/storage/public/investor-day/citi-ID26-ceo-presentation-transcript.pdf) (May 2026)
- [American Banker on Bank of America](https://www.americanbanker.com/news/bank-of-americas-moynihan-touts-good-economics-of-ai) (Jul 2026); [Business Insider on Morgan Stanley](https://www.aol.com/team-engineers-saved-morgan-stanley-094301348.html) (2025); [Retail Banker International on Wells Fargo](https://www.retailbankerinternational.com/news/wells-fargo-job-cuts-ai-rollout/) (Dec 2025)
- [Dev Interrupted on Capital One](https://devinterrupted.buzzsprout.com/1422892/episodes/18459299-how-capital-one-supports-14-000-technologists-with-one-pipeline-ameesh-paleja) (Jan 2026); [Euromoney on Capital One](https://www.euromoney.com/article/2eh2s01l11023kmxtleyo/fintech/prem-natarajan-on-capital-ones-ai-stairway-to-heaven/) (2025)
- [Federal Reserve SR 26-2](https://www.federalreserve.gov/supervisionreg/srletters/sr2602.htm) and [OCC Bulletin 2026-13](https://www.occ.gov/news-issuances/bulletins/2026/bulletin-2026-13.html) (Apr 2026); [Reuters on regulators' AI questions](https://kfgo.com/2026/10/13/exclusive-banking-watchdogs-ramp-up-ai-scrutiny-as-stability-fears-mount-sources-say/) (Oct 2026)

**Spec-driven development**

- [GitHub Spec Kit](https://github.com/github/spec-kit); [Kiro](https://kiro.dev/blog/one-year/); [OpenSpec](https://github.com/Fission-AI/OpenSpec); [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD); [Tessl](https://tessl.io/)
- [Scott Logic: Putting Spec Kit through its paces](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html) (Nov 2025); [Marmelab: The waterfall strikes back](https://marmelab.com/blog/2025/11/12/spec-driven-development-waterfall-strikes-back.html) (Nov 2025)
- [InfoQ: When spec-driven development pays off](https://www.infoq.com/articles/when-spec-driven-development-pays-off/) (Sep 2026); [ESEM 2026 defect study](https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ESEM.2026.84) (Oct 2026); [SpecMine](https://arxiv.org/abs/2608.25202) (Oct 2026); [Asana: Spec-driven development, the good parts](https://asana.com/inside-asana/spec-driven-development) (Sep 2026)
- [Thoughtworks Radar: AGENTS.md](https://www.thoughtworks.com/radar/techniques/agents-md) and [Agent Skills](https://www.thoughtworks.com/radar/techniques/agent-skills)
