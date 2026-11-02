# Agent Instructions

A small collection of instruction sets and rulesets I use with LLMs and agentic tools
(Windsurf, Claude, etc). This repo exists so I can:

- Keep my own working configs in one place
- Reuse them across machines / tools
- Share them with coworkers or the public without context loss

---

## Contents

- `agent.md`  
  General-purpose instruction set for LLMs / agents. Tool-agnostic, meant
  to be pasted or referenced from whatever system I'm using.

- `windsurf/`
  - `README.md` – Notes on how I use Windsurf with these configs
  - `windsurf-agent.md` – Windsurf-specific system prompt / instructions
  - `rules/`
    - `gpt-4-rules.md`
    - `claude-rules.md`
    - `local-model-rules.md`
    - ...etc

(Adjust filenames to match what you actually end up with.)

---

## How I Use This

### General LLMs

- Copy `agent.md` into your system prompt / “profile” / “agent” slot.
- Layer model-specific rules from `windsurf/rules/*.md` or your own equivalents
  on top if your tool supports multiple instruction sources.

### Windsurf / Cascade

High-level pattern:

1. Point Windsurf at this repository.
2. Use `agent.md` as the base instruction file.
3. For specific projects:
   - Add project-local rules in the repo you’re working in
   - Keep the “global” behavior here so it’s shared across projects

See `windsurf/README.md` for any tool-specific notes.

---

## Adapting For Your Own Use

You’re very free to:

- Fork this repo and shape the instructions to your own workflow
- Strip out anything that’s clearly tailored to my environment
- Mix and match: treat `agent.md` as a template, not a holy text

If you do adapt it and keep it public, a note back to this repo as the origin
is appreciated but not mandatory beyond the license terms.

---

## Internal / Company Use

You can use this inside a company environment as a third-party config:

- Treat the contents as pre-existing personal IP
- Keep license / attribution if you copy files into internal repos
- It’s fine to modify locally for team-specific needs

If you’re in a strict environment, check with your OSS/legal folks before
copying anything directly into a company-owned codebase.

---

## License

Copyright © 2025 Faye Emma

This repository is licensed under the Creative Commons Attribution 4.0
International License (CC BY 4.0). See `LICENSE.md` for details.
