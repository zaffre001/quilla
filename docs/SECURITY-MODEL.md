# Security Model

Quilla is a local Unreal Engine debugging bridge for AI agents. It is not a
cloud service, telemetry agent, analytics SDK, or remote code execution service.

This document is intended for public evaluation. It describes the security
boundary, data flow, permissions, and safe evaluation guidance without exposing
private implementation details.

## Summary

- Quilla runs locally on the developer's machine.
- Quilla communicates with Unreal Editor through local process boundaries.
- Quilla does not require external network access for the debugging loop.
- Quilla does not include telemetry, analytics, or automatic update checks.
- Quilla returns only the debugging context requested through explicit agent
  tool calls.
- Quilla release packages include provenance metadata and file hashes.
- Mutating editor actions are separated from read-only inspection actions.

## Trust Boundary

The primary security boundary is the local machine and the Unreal project being
debugged.

```text
AI agent / MCP or DAP client
        |
        | local stdio or debugger protocol
        v
Quilla local runtime
        |
        | local bridge channel
        v
UEDapBridge Unreal Editor plugin
        |
        v
Unreal Editor / PIE / project state
```

Quilla is designed to make Unreal runtime state readable to an agent without
requiring the agent to scrape logs, parse screenshots, or guess from incomplete
editor output.

## Network Behavior

The supported debugging loop is local.

Quilla does not intentionally send project source, assets, runtime state, stack
data, Blueprint data, screenshots, manifests, or logs to Quilla-operated remote
services.

Quilla release packages should be usable in an offline evaluation environment
after the package, Unreal Engine, and the target project are already present on
the machine.

The user's chosen AI agent or MCP client may have its own network behavior.
Quilla does not control those clients. Evaluation teams should review the agent
or client separately.

## Data Flow

Quilla can observe local Unreal debugging state when asked by an MCP/DAP client.
Typical returned data may include:

- Unreal Editor process and bridge status.
- Blueprint breakpoint status.
- Blueprint call stack summaries.
- Captured Blueprint frame and local variable summaries.
- Editor or PIE world summaries.
- Generated debugging artifacts, such as JSON result files or screenshots.
- Structured failure details and suggested next actions.

Quilla is not designed to bulk export a project. Public release documentation
does not describe private implementation protocols or internal field layouts.

## Permission Model

Quilla tools are divided into two broad classes.

Read-only inspection tools observe state and are intended for normal debugging:

- Process and session status.
- Bridge health checks.
- Catalog-backed inspection.
- Blueprint frame and stack inspection.
- World or editor snapshots.
- Debugging artifact collection.

Mutating tools can change the editor session, runtime state, or project files:

- Starting or stopping PIE.
- Installing or removing breakpoints.
- Running editor commands.
- Saving packages.
- Creating smoke-test fixtures.
- Moving actors or changing editor state.

Agents should surface mutating actions to the user before running them,
especially when the target is a production project instead of a disposable test
project.

Use this Permission Model section as the public read-only and mutating tool
guidance for release packages.

## Auditability

Quilla release packages are expected to include `PACKAGE-MANIFEST.json` with
file hashes and build metadata. This helps evaluators confirm which package was
received and which files were included.

Debugging workflows can also produce local artifacts that record:

- Quilla version.
- Unreal Engine version.
- Target project path.
- Plugin binary hash.
- Command status.
- Structured error codes.
- Captured debugging evidence.

These artifacts are intended to make agent debugging actions reviewable instead
of invisible.

## Distribution Integrity

Public release packages should exclude private source and debug material:

- No private server source.
- No private extractor source.
- No Unreal plugin source.
- No tests or fixtures.
- No local build directories.
- No debug symbols unless explicitly provided by the release owner.

Release packages should retain license, third-party notices, setup guidance,
security guidance, and package manifests.

## Evaluation Guidance

For a conservative evaluation:

1. Use a disposable Unreal project or a copy of the target project.
2. Run Quilla on a local machine or isolated virtual machine.
3. Disable external network access if the evaluation requires it.
4. Inspect `PACKAGE-MANIFEST.json` before running the package.
5. Start with read-only inspection tools.
6. Require user approval before mutating editor or project state.
7. Review generated artifacts after the debugging run.

## Known Risks

Quilla intentionally gives an agent structured access to local debugging state.
That access is useful, but it also means the evaluator should treat Quilla like
other local developer tools that can inspect or affect a project.

Known risks include:

- The chosen AI agent may send prompts or tool results to its own service.
- Mutating tools can change editor state or project files.
- Screenshots or JSON artifacts may contain project-specific information.
- A misconfigured package or project plugin can fail to connect or target the
  wrong editor process.
- A release package should not be redistributed unless the license allows it.

Quilla's security posture is based on local execution, explicit tool calls,
package provenance, and clear separation between read-only and mutating actions.

## Non-goals

Quilla is not a sandbox for arbitrary AI agents. It does not replace a company's
source-control policy, endpoint security, secret-scanning policy, or AI usage
policy.

Quilla also does not claim to prevent every possible reverse-engineering attempt
against a distributed binary package. The source tree and private implementation
details remain private to reduce that risk.
