---
layout: post
title: "Credential Brokering Operator's Guide"
date: 2026-11-03 10:00:00 -0500
categories: jekyll update
---

## Credential Brokering 101

When an AI agent acts on your behalf in Jira, Datadog, Notion, or Slack, it needs credentials. The tempting move is to hand it an API key and move on. Credential brokering is the alternative: the agent never holds a long-lived secret. A broker holds the secrets and either gives the agent a short-lived token or makes the call on the agent's behalf.

At my last company, I built an orchestration layer for agents doing quality engineering work: triaging test failures, writing test plans, grading risk. They ran headless, so there was no guarantee a human was watching. This guide covers the pattern I used, how I rolled it out, and where it stops being enough.

### Why Not Just Give the Agent a Key?

- **Agents are non-deterministic.** You can't fully predict which calls an agent will make, so you can't fully predict what a standing key will be used for.
- **Long-lived keys leak.** Logs, transcripts, and context windows are all places a secret can end up.
- **A key in context is a key the model can be talked into misusing.** Anything the agent reads can influence what it does with what it holds.

Brokering was the only approach that passed our rapid risk assessment.

## The Pattern

### Secrets Live in a Vault

Third-party secrets live in **Doppler**. Agents never read from the vault directly. Only the orchestration layer can.

### The Orchestration Layer Brokers Every Call

No agent ever receives a third-party key. When an agent starts a task that touches a third-party system, the orchestration layer pulls the secret from Doppler and does one of two things, depending on what the provider supports:

- **The provider issues short-lived tokens (Atlassian, via OAuth):** the orchestration layer mints a time-boxed access token and hands the agent only that token.
- **The provider only issues long-lived keys (Datadog, Notion, Slack):** the orchestration layer makes the API call itself, attaching the key on the agent's behalf. The agent asks for the action; it never sees the key.

I modeled the design on AWS AgentCore's approach to agent identity, so the agent's access is always delegated and always bounded.

### Tokens Expire Where the Provider Allows It

On Atlassian, every token is time-boxed. When it expires, the agent goes back through the broker. There is no standing access to forget about or clean up later.

💡 **Protip:** keep token lifetimes close to the length of the task. A token that outlives its task is a standing credential with extra steps.

On Datadog, Notion, and Slack, the key itself never expires. Keeping it out of the agent's reach is what protects it, but the key is still standing access, held by the orchestration layer. If you can choose your providers, prefer the ones that support short-lived, scoped tokens.

## Rolling It Out

### Prove It on One System

I started with **Atlassian** as the proof of concept. One integration is enough to shake out the broker's design: where secrets are fetched, how tokens are minted, what happens on expiry, and what the agent sees when access is denied.

### Repeat Per Integration

Once the pattern held, I extended it to **Datadog**, **Notion**, and **Slack**. The broker stays the same; each integration brings its own auth quirks and scopes. For these three, that meant brokering the call instead of the token.

💡 **Protip:** scope each grant to the task, not the integration. "Read this Jira project" is safer than "Jira access."

## Where Brokering Stops

Brokering solves *who holds the keys*. It does not solve *what the agent does with them*.

### The Confused Deputy

A confused deputy is a program with legitimate authority that gets tricked into using it for someone else. An agent holding a valid token reads a poisoned document, a malicious issue, or a hostile calendar invite, and follows the instructions inside it. Every identity check passes, because the token is real. Authentication was never the problem.

### Prompts Are Suggestions, Not Permissions

My first line of defense was guardrails in the system prompt. That was a stopgap. A model can be talked around a prompt; it can't be talked around a policy enforced outside the agent, on every call.

### Per-System Guardrails Can't See Across Systems

Each guardrail I built was per-system: Jira, then Datadog, then Notion. What I couldn't enforce was a policy spanning them, like *"if this session read something private in Notion, don't post it to Slack."* Each system only sees its own calls. The dangerous patterns are combinations across systems.

Enforcing that takes a layer that sits between the agent and every server it talks to, such as an MCP gateway, and evaluates policy on every call with the full session in view.

## Checklist

- Secrets stay in a vault the agent can't read.
- No agent ever holds a third-party key.
- Where the provider supports it, agents get short-lived, task-scoped tokens from a broker; renewal goes back through the broker.
- Where it doesn't, the broker makes the call and the key never leaves it.
- Prove the pattern on one integration before repeating it.
- Treat prompt guardrails as a stopgap, not a control.
- Enforce cross-system policy per call, outside the agent.
