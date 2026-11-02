# How leading firms run coding agents

*Research summary, 2 November 2026, compiled with Claude from public sources. Most of the figures below are self-reported by the companies and haven't been audited, and a few come from press coverage of conference talks.*

## Bottom line

At the companies furthest along, coding agents mostly run in the background. They work in sandboxes that look like an engineer's dev environment, CI checks their output, and a person reviews the pull request before it merges. Where these companies use orchestration, they size it to the job instead of layering it on by default.

At several of them, agents already produce most of the code. Ramp credits its agent with 74% of merged pull requests, Stripe merges more than 1,300 agent-written PRs a week, and Anthropic says Claude writes over 80% of its merged code. Those numbers show adoption. They say little about quality, and the figures that would (revert rates, escaped defects, total cost) are mostly unpublished.

What these teams share is heavy investment in the environment: realistic sandboxes, checks that finish in seconds, and review at merge. Structure around the agent shows up where it protects intent, handles real uncertainty, or coordinates independent pieces of work, as with Stripe's blueprints and Airbnb's migration pipeline. Agents handing work to other agents is much rarer. Even Cognition and Cursor, the vendors most enthusiastic about agent swarms, say one agent should do the writing while any others research or review.

Every leading team writes short plans and treats passing tests as the definition of done. None of the firms profiled here describes using a spec-driven development (SDD) framework. The best studies so far find these frameworks add review time without reducing bugs, although specs did make it easier for reviewers to trace problems back to requirements.

Large banks have given coding assistants to tens of thousands of developers. The controls they talk about publicly are sandboxes, spending caps and human review. Regulators are asking about agent permissions, audit trails, oversight and how agents would be shut down, and the usual security and change-management obligations still apply.

## The common pattern

Most of the leaders have settled on the same basic shape. One agent takes one task, works in the same environment an engineer would use, checks its own work, and hands a pull request to a person.

1. Work starts where it already lives. Engineers kick off agents from Slack, a ticket or a web page (Stripe, Ramp, Spotify, Shopify, Linear).
2. The agent runs in a real dev environment. Sandboxes are pre-warmed, hold the full stack and can't reach production. Stripe reuses the devboxes its engineers work in, and Uber's agent platform grew out of its remote dev environments.
3. One agent writes the code and tests it. It edits, runs lint and tests, and fixes whatever fails, while fixed steps like linting, pushing and CI run around it. Stripe caps this at two CI rounds.
4. A person approves the merge. Each PR is credited to whoever asked for the work, and someone else reviews it. Spotify lets low-risk fleet changes merge automatically, but that's the exception.

A few ideas sit behind that shape.

- Keep things as simple as the work allows. Anthropic recommends the [simplest solution that works](https://www.anthropic.com/research/building-effective-agents). Cognition now says multi-agent setups work when a single agent writes and the rest research or review, and Cursor credits many of its gains to [removing complexity](https://cursor.com/blog/scaling-agents). Teams get parallelism by running many independent sessions, one per task.
- Harnesses get smaller as models improve. Anthropic strips parts out of its harnesses as models get better, Spotify reportedly dropped its LLM judge, and specs help weak models much more than strong ones.
- Context stays short and sits in the repo. AGENTS.md works as a map, Stripe scopes its rules by directory, and Cloudflare generated AGENTS.md files for about 3,900 repositories.
- Review has become the constraint. OpenAI says the limit is human attention, Ramp says it's judgment about what to ship, and Spotify now has 76% more PRs to review. That's pushing money into verification: AI reviewers at Uber, Cloudflare and Cursor, hidden verifiers at Spotify and real-browser checks at Ramp.
- Pipelines suit fleet work. State-machine pipelines show up where the work is mechanical and easy to check, like Airbnb's test migration and Spotify's Fleet Management. The big multi-agent "swarm" successes were new projects with near-perfect test suites, which existing codebases rarely have.

## Where orchestration helps

The pipelines that work leave the mechanical steps to ordinary code and give one capable agent room to reason. In each case, a pass/fail check decides when work moves forward.

| Pipeline | Unit of work | Handled by code | Handled by the agent | Gate and cap |
| --- | --- | --- | --- | --- |
| [Stripe Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) | One task or ticket | Linting, autofixes, git push and CI runs | Implementing the task and fixing CI failures | A local lint loop, then at most two CI rounds before a person takes over |
| [Airbnb test migration](https://medium.com/airbnb-engineering/accelerating-large-scale-test-migration-with-llms-9565c208023b) | One test file | The sequence of steps (refactor, Jest, lint, TypeScript), lint autofix, and gathering up to 50 related files as context | Rewriting the file and fixing each step's errors | A file only advances when its step passes. Each step has a retry limit, and the hardest files took 50–100 attempts |
| [Spotify Honk](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) | One change in one repo | Choosing target repos and opening PRs through Fleet Management, and running the verifiers | Making the code change | Verifiers sit behind a single tool, and a stop hook blocks the PR until they pass |

Airbnb's results show why this suits mechanical work. Three quarters of the files passed in the first four-hour run, 97% passed after four days of tuning against sampled failures, and engineers finished the last 3% by hand. The team found that brute-force retries and picking the right context files mattered more than polishing the prompt.

None of the three hands work from one agent to another. It helps to think of the system as doing three separate jobs:

1. Preserving intent: what to build and why, the invariants, and what success looks like. Plans, specs and tests carry this.
2. Choosing how to execute, which should adapt to how ambiguous, risky and large the task is.
3. Verifying the result and deciding who can accept it, through repeatable checks plus independent or human sign-off where it matters.

## Firm by firm

The tables below cover what each company has described publicly.

### Product and fintech companies

| Company | What they built | How work is gated | Reported scale |
| --- | --- | --- | --- |
| Ramp | [Inspect](https://ramp.com/blog/introducing-inspect), which runs OpenCode in [Modal sandboxes](https://modal.com/blog/how-ramp-built-a-full-context-background-coding-agent-on-modal) that hold the full stack, and can take screenshots, run tests and read telemetry. People start it from Slack, GitHub, Linear, the web or a phone | Each PR is credited to the person who asked for it, so someone else has to approve it | 74% of merged PRs (Oct 2026). Ramp opened Inspect to other companies the same month |
| Stripe | [Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents), one-shot agents built on a fork of Block's goose that run on the same pre-warmed devboxes engineers use | Code-defined [blueprints](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2) run fixed steps like lint and push around the agent's work. After at most two CI rounds, a person reviews | 1,300+ merged PRs a week containing no human-written code (Feb 2026) |
| Spotify | [Honk](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1), which runs Claude through the Agent SDK inside Spotify's own harness and plugs into its Fleet Management migration system | Verifiers sit behind one tool, and a [stop hook](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3) blocks the PR until they pass. Safe fleet changes merge automatically | About 1,000 merged PRs every 10 days (Mar 2026), and a backend Java migration finished in [three days](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) |
| Shopify | [River](https://shopify.engineering/under-the-river), a Slack agent that only works in public channels, built on a shared internal agent platform. [Roast](https://shopify.engineering/introducing-roast) handles scripted workflows | Production code still needs human review | River coauthors 1 in 8 merged PRs (May 2026) |
| Uber | [uReview](https://www.uber.com/blog/ureview/), an AI code reviewer, and Minion, a background-agent platform that grew out of Uber's remote dev environments | Engineers review and approve every agent change | uReview covers over 90% of ~65,000 weekly diffs, and agents opened [11% of merged PRs](https://background-agents.com/summit/sessions/nikhil-ramakrishnan/) (May 2026) |
| Airbnb | A [test migration pipeline](https://medium.com/airbnb-engineering/accelerating-large-scale-test-migration-with-llms-9565c208023b) that moved each file through refactor, Jest, lint and TypeScript steps, with retries | A file only moved on once its checks passed | About 3,500 files in 6 weeks against an estimated 1.5 years by hand (Mar 2025). By May 2026, [60% of new code was AI-written](https://techcrunch.com/2026/05/08/airbnb-says-ai-now-writes-60-of-its-new-code/) |
| Coinbase | Cursor and Copilot became mandatory in 2025, and background agents now work tickets | Core cryptography code is still reviewed line by line | An executive says 95–100% of code is now written by or with LLMs ([Jul 2026](https://cointelegraph.com/news/over-95-of-coinbases-code-is-now-written-with-ai)) |

### AI labs and dev-tool companies

| Company | How they build | How work is gated | Reported scale |
| --- | --- | --- | --- |
| OpenAI | A [harness engineering](https://openai.com/index/harness-engineering/) team of three, later seven, built about a million lines without hand-writing any code. Their AGENTS.md is a ~100-line map, and plans live in the repo. [Symphony](https://openai.com/index/open-source-codex-orchestration-symphony) runs one Codex agent per ticket | Custom linters and structural tests whose error messages tell the agent how to fix the problem. Cleanup agents counter drift, and people review | About 1,500 PRs, or 3.5 per engineer per day (Feb 2026). Symphony raised landed PRs by about 500% for some teams |
| Anthropic | Claude Code is used across engineering, usually with parallel sessions, a plan first, and a check for Claude to run ([best practices](https://code.claude.com/docs/en/best-practices)) | Tests are the check, and a separate skeptical evaluator works better than self-evaluation ([harness design](https://www.anthropic.com/engineering/harness-design-long-running-apps)) | Claude writes 80%+ of merged code ([May 2026](https://www.anthropic.com/institute/recursive-self-improvement)) |
| Cloudflare | An [internal stack](https://blog.cloudflare.com/internal-ai-engineering-stack/) built mostly on OpenCode, with AGENTS.md generated for about 3,900 repos. [Code Mode](https://blog.cloudflare.com/code-mode/) has models write code against typed APIs instead of calling tools | [AI code review](https://blog.cloudflare.com/ai-code-review/) uses a coordinator and up to 7 specialist reviewers at about $1 per review. People still review as well | Weekly merge requests rose from about 5,600 to more than 8,700 (Apr 2026) |
| Linear | [Linear Agent](https://linear.app/now/how-we-built-linear-agent) is one constrained agent with skills and approval checks. Agents take on issues as delegates | People stay accountable. Linear's [guidelines](https://linear.app/developers/aig) say an agent can't be held accountable | Almost 700 agent PRs merged internally in a month (Jun 2026) |
| Cursor | Cloud agents run in VMs, and [Bugbot](https://cursor.com/blog/building-bugbot) reviews all internal code | Bugbot's fix rate went from 52% to over 70% after it moved to a single agent loop | Agents created 35% of internally merged PRs ([Feb 2026](https://cursor.com/blog/third-era)) |
| GitHub | [Agent HQ](https://github.blog/news-insights/company-news/welcome-home-agents/) runs Copilot, Claude and Codex agents side by side | The Copilot CLI team scaled back delegation to subagents and saw 23% fewer tool failures ([Jun 2026](https://github.blog/ai-and-ml/how-we-made-github-copilot-cli-more-selective-about-delegation/)) | Octoverse reports that over 40% of PRs were agent-started by September 2026, up from 17% in March ([Octoverse](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)) |
| Cognition | Devin. Its ["Don't Build Multi-Agents"](https://cognition.com/blog/dont-build-multi-agents) post (2025) was revised by ["Multi-Agents: What's Actually Working"](https://cognition.com/blog/multi-agents-working) (Apr 2026) | Other agents can research or review, but only one writes code | 659 Devin PRs merged internally in one week (Feb 2026) |

These numbers track adoption, and they aren't directly comparable. "AI-written code" and "PRs written entirely by an agent" measure different things, and no company publishes revert or incident rates for agent PRs.

## Regulated peers

The big banks have moved coding assistants out of pilots and into the hands of tens of thousands of developers. When they describe controls in public, they talk about isolation, spending caps and human review. Orchestration frameworks don't come up.

| Bank | Scale | Claimed result | Agents and controls |
| --- | --- | --- | --- |
| JPMorgan Chase | 65,000+ technologists ([American Banker](https://www.americanbanker.com/news/2026-the-most-powerful-women-in-banking-5-lori-beer-jpmorganchase)) and about 8,000 Claude licences (Aug 2026) | The coding phase is 10–20% faster, and the CIO says most code is now written or assisted by agents (2026) | "Devspace" keeps AI tools away from employee credentials and internal systems, and some engineers have a $2,000/month cap on Claude Code ([Sep 2026](https://www.newsquawk.com/headlines/jpmorgan-jpm-introduced-usd-2k-monthly-claude-code-spending-limits-for-some-engineers-and-is-rolling-out-a-more-secure-devspace-environment-business-insider-reports)) |
| Goldman Sachs | About 12,000 engineers ([Fortune](https://fortune.com/2026/05/08/goldman-sachs-cio-marco-argenti-tech-ai-future-of-work-employees/)) | Up to 20% from its developer copilot (May 2025) | A Devin pilot under human supervision ([Jul 2025](https://www.nbcchicago.com/news/business/money-report/goldman-sachs-is-piloting-its-first-autonomous-coder-in-major-ai-milestone-for-wall-street/3787263/)). The agents it built with Anthropic in 2026 target operations rather than coding |
| Citi | 30,000 developers with AI coding tools ([Jan 2025](https://finance.yahoo.com/news/citi-deploys-ai-coding-tools-152606091.html)) | AI-assisted code review frees up about 100,000 hours a week ([Investor Day, May 2026](https://www.citigroup.com/rcs/citigpa/storage/public/investor-day/citi-ID26-ceo-presentation-transcript.pdf)) | Scaling agents across the firm after a 5,000-user pilot (Sep 2025) |
| Bank of America | 19,000 developers ([Jul 2026](https://www.americanbanker.com/news/bank-of-americas-moynihan-touts-good-economics-of-ai)) | Developer productivity up more than 20% | None disclosed |
| Morgan Stanley | 15,000 developers with access to its in-house DevGen.AI (Jun 2025) | 9 million lines of legacy code reviewed and about 280,000 hours saved (2025) | The AI writes plain-English specs of old code, and developers write the new code ([Business Insider](https://www.aol.com/team-engineers-saved-morgan-stanley-094301348.html)) |
| Wells Fargo | Not disclosed | Engineers are 30–35% more efficient at writing code ([Dec 2025](https://www.retailbankerinternational.com/news/wells-fargo-job-cuts-ai-rollout/)) | None disclosed |
| Capital One | About 14,000 technologists on one delivery pipeline ([Jan 2026](https://devinterrupted.buzzsprout.com/1422892/episodes/18459299-how-capital-one-supports-14-000-technologists-with-one-pipeline-ameesh-paleja)) | No coding figures disclosed | Staged pilots, risk tiers based on consequences, and humans in the loop for high-risk decisions ([Euromoney](https://www.euromoney.com/article/2eh2s01l11023kmxtleyo/fintech/prem-natarajan-on-capital-ones-ai-stairway-to-heaven/)) |

None of the banks say how they measure productivity, so the percentages can't be compared.

On regulation, [SR 26-2](https://www.federalreserve.gov/supervisionreg/srletters/sr2602.htm) and [OCC Bulletin 2026-13](https://www.occ.gov/news-issuances/bulletins/2026/bulletin-2026-13.html) replaced SR 11-7 in April 2026 and left generative and agentic AI out of model risk guidance while regulators gather input through a request for information. In October the Fed, OCC and FDIC started informally asking banks about agent permissions, audit trails, human oversight and how they would shut agents down ([Reuters](https://kfgo.com/2026/10/13/exclusive-banking-watchdogs-ramp-up-ai-scrutiny-as-stability-fears-mount-sources-say/)). Nothing published so far asks for controls between agent steps. Security, access, operational-risk and change-management obligations still apply, though. The case for putting controls where consequences land (credential and data access, merge, deploy) rests on engineering and risk grounds. No specific regulation requires it.

## Where spec-driven development fits

Writing down what you want before an agent starts coding is standard practice. Mandatory, heavyweight SDD frameworks are another matter. The label covers three quite different things: durable intent and acceptance criteria, lightweight plans or spec deltas for a single task, and persona-based multi-stage frameworks like BMAD. The evidence against is strongest for the last of these, and the frameworks themselves have been getting lighter.

| Tool | Approach | Status, Nov 2026 |
| --- | --- | --- |
| [GitHub Spec Kit](https://github.com/github/spec-kit) | Spec, plan and task templates that work with many agents | Reached v1.0 in Aug 2026 and has about 143k GitHub stars. Thoughtworks rates it "Assess" |
| [Kiro](https://kiro.dev/blog/one-year/) (AWS) | An IDE that writes requirements, design and task specs | Reached 250k developers in its first three months, added a quick mode without approval gates, and has shifted its 2026 focus to platform features |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) | Proposals and spec deltas for each change | v1.0 (Jan 2026) swapped its fixed sequence for flexible actions. About 73k stars, rated "Assess" |
| [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) | A persona-based agile workflow with an analyst, PM, architect and developer | v7 is still in alpha, the core skills went from 14 to 8, and Thoughtworks calls it more rigid than OpenSpec |
| [Tessl](https://tessl.io/) | Specs as the source of truth, with a spec registry | The spec framework no longer appears on its site, which now leads with agent skills |

Practitioner tests point to a big review cost. In one comparison, a Spec Kit feature took about 3.5 hours to review and produced 2,577 lines of Markdown, while the same goal with plain prompting took about 24 minutes ([Scott Logic](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)). Another test produced 1,300 lines of text for a small date-display feature ([Marmelab](https://marmelab.com/blog/2025/11/12/spec-driven-development-waterfall-strikes-back.html)).

A small study found that giving reviewers a spec didn't help them find more bugs (recall of 0.525 versus 0.518) and stretched review from 27 to 48 minutes. It did help them trace problems back to requirements, with attribution rising from 0.00 to 0.81. Specs also lifted a weak model by about 21 points but a strong one by only about 2 ([InfoQ, Sep 2026](https://www.infoq.com/articles/when-spec-driven-development-pays-off/)).

Two independent studies look at specs in the wild. One, covering 88,052 pull requests, found that spec artifacts didn't reduce defects, though it studied linked tickets rather than output from SDD tools ([ESEM 2026](https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ESEM.2026.84)). The other counted 470,795 spec files across 73,030 public repos and found that 81.5% were committed only once and 98.3% had a single author, which suggests specs tend to get written and then left alone ([SpecMine, Oct 2026](https://arxiv.org/abs/2608.25202)).

Company reports are uncontrolled but still useful. Asana tried Spec Kit and OpenSpec and neither stuck, but a lighter spec skill it built in-house did, and its seven heaviest users merged 38% more PRs a week ([Asana](https://asana.com/inside-asana/spec-driven-development)). So specs can help in the right form. No large company has published controlled results from any SDD framework, although some may be using one without saying so.

Analysts are cautious as well. Thoughtworks rates Spec Kit and OpenSpec "Assess," while [AGENTS.md](https://www.thoughtworks.com/radar/techniques/agents-md) and [Agent Skills](https://www.thoughtworks.com/radar/techniques/agent-skills) sit one ring closer to adoption at "Trial."

Leading teams lean on lighter tools instead. OpenAI's Codex team keeps its AGENTS.md to about 100 lines, calling it "a map, not a 1,000-page instruction manual," and checks design docs and plans into the repo ([OpenAI](https://openai.com/index/harness-engineering/)). OpenAI's [ExecPlans](https://developers.openai.com/cookbook/articles/codex_exec_plans) are a single plan document per task that the agent keeps up to date, and Anthropic's long-running agents track a JSON feature list with pass/fail flags alongside a progress file ([Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)). Claude Code's guidance pairs a short CLAUDE.md and plan mode with failing tests the agent has to make pass ([Claude Code docs](https://code.claude.com/docs/en/best-practices)), and Spotify writes migration prompts as end states that tests can verify.

In practice, every leading team keeps the useful core of SDD: a short plan and tests that define done. More structure may well be worth it for ambiguous or high-stakes work, but nobody has tested that properly either. For now the evidence doesn't justify making heavyweight SDD mandatory for all work, and specs seem to matter less as models get stronger.

## Implications for regulated enterprises

Designs that lean heavily on orchestration, with a state machine coordinating separate agent sessions behind spec frameworks and rules-engine gates, put controls in sensible places at the edges. The trouble is in the middle, where work passes from one agent to the next, and that's where they part ways with the leaders.

| Design choice | What the leaders do | Assessment |
| --- | --- | --- |
| Deterministic steps around agents | Stripe's blueprints, Airbnb's per-file pipeline, Shopify's Roast and Spotify's verifiers all wrap a single agent in fixed steps | Matches the leaders, as long as each task gets one agent |
| A separate agent session for each stage, passing structured outputs along | One agent carries a task from start to finish with the full context in its prompt | Differs. Each handoff loses context, and instructions drift as they're relayed |
| SDD frameworks as the entry point | A short AGENTS.md, plans in the repo, and tests as the definition of done | Differs. The review cost has been measured, and a defect benefit hasn't |
| Rules engines deciding whether to pass, retry or escalate | CI, linters and stop hooks inside the loop, with human review at merge. Spotify reportedly dropped its LLM judge as models improved | Most useful at merge, deploy and data access. Between reasoning steps the cost rarely pays back |
| Evidence collected at every step | PR history, CI logs and session transcripts. Shopify only runs agents in public Slack channels | Same goal. Regulators are asking about permissions, audit trails, oversight and shutdown |
| A central platform driven from a CLI | Background agents started from Slack, tickets or the web, running in the same environments engineers use | Partly. A central platform makes sense, but a pipeline someone runs from a CLI keeps that person waiting on it |

The research points to these priorities:

1. Build an agent-ready sandbox. Give agents the environment engineers already use, cut off from production and personal credentials, the way Stripe's devboxes and JPMorgan's Devspace are.
2. Make feedback fast inside the loop, with lint and test results in seconds, a hook that won't let the agent call itself done until checks pass, and a cap on CI rounds.
3. Put gates at the boundaries. That means human review at merge with separation of duties (Ramp credits each agent PR to the person who asked for it), plus tight controls on deploys and data access.
4. Keep context short and in the repo, with AGENTS.md as a map, skills that load on demand, and plans checked in.
5. Start where pipelines clearly help, with fleet-wide migrations and routine maintenance (Spotify, Airbnb), then move on to background agents for feature work (Ramp, Stripe).
6. Measure verified outcomes such as review time, human interventions per accepted change, and revert and escaped-defect rates. No leader has published these for agent PRs yet.
7. Test before mandating anything. Run the same real tasks two ways with the same models and environment, once with a lightweight agent loop and a plan where useful, and once with a prescribed multi-stage workflow. Compare how well each meets the intent, elapsed time and cost, reviewer burden, interventions and post-merge defects, broken down by how ambiguous and risky the tasks are.

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
