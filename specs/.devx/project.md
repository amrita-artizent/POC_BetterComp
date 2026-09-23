# POC_Better_Comp

> Auto-generated project context for AI-assisted development.
> Last updated: 2026-09-23

**Organization:** delta-studio

## Overview

Develop a Proof of Concept (POC) for an agentic AI solution that enables users to access business insights through natural language text or voice.

## Development Methodology

This project follows **Spec-Driven Development (SDD)** with **Test-Driven Development (TDD)**.

Every feature has:
- `specs.md` — Full technical specification
- `requirements.md` — Implementation acceptance checklist
- `tdd-tests.md` — Test specifications (Red → Green → Refactor)
- `prompt.md` — Ready-to-use implementation prompt

## Features (1)

- **Compatibility Assessment for Required Platforms; As a user, I want to use Compatibility Assessment for Required Platforms so that I can benefit from its core functionality; As an Executive user, I want to ask plain-English questions against approved Gold layer data so that I can get governed manufacturing insights without using unapproved datasets (+190 more)** (193 user stories)

## Getting Started

1. Read this file for project context
2. Check `specs/.devx/workflow.md` for the development workflow
3. Review `specs/.devx/instruction.md` for architecture and multi-repo rules
4. Review `specs/.devx/feature-routing.json` for suggested owning and impacted repos
5. Pick a feature from `specs/.devx/features.json`
6. Open the feature's `prompt.md` and use it with your AI assistant
7. Follow the spec and requirements to implement

## Project Structure

```
specs/
  .devx/
    project.md          ← You are here
    workflow.md          ← Development workflow
    features.json        ← Feature index (machine-readable)
    tracker.json         ← Code-generation execution status
    generation.json      ← Last generation metadata
    feature-routing.json ← Suggested repo routing
    architecture.md      ← System architecture
    init.sh              ← Setup AI tool configs
  <feature-slug>/
    specs.md             ← Technical specification
    requirements.md      ← Implementation acceptance checklist
    tdd-tests.md         ← TDD test specifications
    prompt.md            ← Implementation prompt
```

## AI Tool Setup

Run the init script to configure your AI tools automatically:

```bash
bash ./specs/.devx/init.sh
```

If you want execute permissions as well:

```bash
  chmod +x ./specs/.devx/init.sh && ./specs/.devx/init.sh
```

The script lists supported AI tools, lets you choose one, and creates only that tool's config files.
