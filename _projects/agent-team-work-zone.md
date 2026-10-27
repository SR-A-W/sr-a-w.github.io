---
layout: page
title: Agent Team Work Zone
description: A persistent management layer for long-lived Claude Code agent teams, designed to preserve working state across compaction and restarts.
img: assets/img/projects/agent-team-work-zone/session-persistence.png
importance: 1
category: open-source
related_publications: false
github: https://github.com/SR-A-W/agent-team-work-zone
---

**Built specifically for Claude Code, Agent Team Work Zone extends its native Agent Teams with a persistent, easy-to-use workflow—so you can get more from Claude Code on complex, long-running projects.**

## The Persistence Problem

Claude Code agents can now work as teams, but the teams themselves are still ephemeral. Teammate state lives inside a session and may disappear when a terminal closes, a process restarts, or a context window is compacted. On work that spans days or weeks, the human is forced to become the team's memory and scheduler: reconstructing roles, restating decisions, and rewriting handoff prompts after every interruption.

Agent Team Work Zone (ATWZ) is a filesystem-based operations layer for long-lived Claude Code agent teams. It does not attempt to keep every agent process alive forever. Instead, it makes the information required to rebuild a team durable.

<figure class="project-figure project-figure--narrow">
  <img src="{{ '/assets/img/projects/agent-team-work-zone/system-overview.png' | relative_url }}" alt="Agent Team Work Zone system overview">
  <figcaption>Agents remain ephemeral, while a durable workspace preserves the operational state needed to reconstruct the team.</figcaption>
</figure>

## Files as Operational Memory

ATWZ gives each agent a persistent workstation containing its role, working context, decisions, commitments, task files, and recovery notes. Team rosters and messages live beside the project rather than being trapped in a closed conversation. The guiding principle is simple: if a future agent needs it, write it to disk.

This separates volatile in-context memory from durable project state. Context remains useful as a fast working surface, but files become the source for recovery, inspection, versioning, and handoff. Each workstation is owned by one agent, which keeps the system low-coupling and prevents teammates from silently reorganizing one another's state.

<figure class="project-figure project-figure--narrow">
  <img src="{{ '/assets/img/projects/agent-team-work-zone/files-vs-context.png' | relative_url }}" alt="Comparison of volatile context and durable file-based agent state">
  <figcaption>ATWZ treats the context window as working memory and the project workspace as durable operational memory.</figcaption>
</figure>

## Checkpoint, Recovery, and Communication

On top of the file substrate, ATWZ adds automatic, non-destructive checkpoints. An age-based idle hook detects stale working state and asks an agent to refresh its checkpoint before leaving, while failing open when it cannot establish the state safely. A one-command reactivation flow then reads the team registry, respawns each role, and lets every new teammate recover from its predecessor's checkpoint.

<div class="project-figure-grid project-figure-grid--two">
  <figure class="project-figure">
    <img src="{{ '/assets/img/projects/agent-team-work-zone/checkpoint-gate.png' | relative_url }}" alt="ATWZ automatic checkpoint gate">
    <figcaption>An idle-time checkpoint gate keeps recovery state fresh without overwriting user work.</figcaption>
  </figure>
  <figure class="project-figure">
    <img src="{{ '/assets/img/projects/agent-team-work-zone/reactivation.png' | relative_url }}" alt="ATWZ team reactivation flow">
    <figcaption>Reactivation reconstructs the roster and lets each fresh teammate resume from durable state.</figcaption>
  </figure>
</div>

Agents in different sessions coordinate through two file-based channels: a meeting room shared by flat agents and team leads, and a per-team roundtable for internal coordination. Messages have explicit lifecycle states and authorship, so task assignment and completion remain auditable after the original sessions close.

<figure class="project-figure project-figure--narrow">
  <img src="{{ '/assets/img/projects/agent-team-work-zone/communication.png' | relative_url }}" alt="ATWZ file-based communication lifecycle">
  <figcaption>File-based messaging turns cross-session coordination into a durable, inspectable workflow.</figcaption>
</figure>

## Design Manual and Lessons

The accompanying paper is a developer-oriented design manual rather than a benchmark study. It documents the architecture, lifecycle, upgrade boundaries, known limitations, and failure modes discovered while the system was used to build itself. The project frames persistent agent state as infrastructure for reducing **agentic technical debt**: the accumulated cost of lost decisions, repeated prompts, ambiguous ownership, and brittle recovery in multi-session software work.

- [Paper](https://arxiv.org/abs/2607.22917)
- [Code](https://github.com/SR-A-W/agent-team-work-zone)

- [npm package](https://www.npmjs.com/package/agent-team-work-zone)
