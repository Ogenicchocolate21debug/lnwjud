---
name: ogenic-chatgpt-remote-mcp
description: Use when operating lnwjud from ChatGPT web/mobile through Remote MCP or OpenAI Secure MCP Tunnel. Prefer read-before-write, verify the exact workspace/project, use minimal-scoped tools, keep credentials local, and confirm risky actions.
---

# OGENIC ChatGPT Remote MCP for lnwjud

## Goal

Turn ChatGPT web/mobile into an operator console for lnwjud without pretending the phone itself is the execution machine.

```text
ChatGPT web/mobile
  -> MCP connection
  -> Remote MCP (HTTPS + OAuth) OR OpenAI Secure MCP Tunnel
  -> lnwjud local MCP
  -> exact active project/workspace
  -> tool execution
```

## Operating contract

1. Identify the exact target project/workspace.
2. Read current state before mutation.
3. Prefer the smallest reversible change.
4. For file/code work: inspect -> diff -> patch -> verify.
5. For shell/process work: use bounded commands, capture stdout/stderr, verify exit status.
6. For browser/UI work: list targets first, select the exact target, then act.
7. Never expose API keys, OAuth tokens, cookies, refresh tokens, tunnel credentials, or raw secret material.
8. Never claim a write/deploy succeeded until a follow-up read or status check confirms it.
9. Destructive actions require explicit user intent.
10. If a requested capability is unavailable, return `CAPABILITY_REQUIRED` and continue any independent safe work.

## Connection selection

### Preferred: Remote MCP + OAuth

Use lnwjud `Settings -> Remote MCP & Tunnel` and start Remote MCP. Add the returned HTTPS `/mcp` URL to ChatGPT and authorize with OAuth/pairing code. Do not paste secrets into chat.

### Alternative: OpenAI Secure MCP Tunnel

Reuse the existing tunnel identity and reconnect lnwjud to that same tunnel after local restarts. Do not recreate tunnel identity merely because the local MCP port changes.

## Mobile behavior

The phone is the operator surface. Execution remains on the machine/runtime running lnwjud. Do not describe iOS as executing Windows-only lnwjud tools locally.

## First validation

Run a read-only smoke test before writes:

```text
1. list active workspace/project
2. list available tools
3. read one harmless file
4. run a non-mutating status command
```

Only after all four pass should write/execute operations be enabled.

## Mutation validation

```text
BEFORE -> exact target + current state
PATCH  -> minimal change
AFTER  -> read/status verification
REPORT -> changed / unchanged / failed
```

## Failure modes

- Connection missing -> `CAPABILITY_REQUIRED: MCP_CONNECTION`
- Wrong workspace -> stop before mutation
- Authentication expired -> re-authorize; never bypass auth
- Local lnwjud runtime offline -> report runtime unavailable
- Tunnel/Remote MCP unavailable -> do not substitute cookie/session extraction
- Ambiguous target -> report ambiguity instead of guessing
