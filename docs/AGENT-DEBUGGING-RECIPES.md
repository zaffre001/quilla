# Agent Debugging Recipes

[한국어](AGENT-DEBUGGING-RECIPES.ko.md)

These recipes are written for MCP-capable coding agents using a local Quilla
release package. Prefer compact status and diagnosis calls first, then request
larger artifacts only when they are needed for a concrete debugging action.

Tool names can vary slightly by agent integration. Use the closest Quilla MCP
tool exposed by the package.

## First-run Setup

Goal: prepare a project so the agent can launch Quilla and talk to Unreal
Editor.

Order:

1. Verify the package layout and `docs/PACKAGE-MANIFEST.json`.
2. Run `tools/quilla-first-run.ps1` with the target `.uproject` and Unreal
   Engine 5.5 root.
3. Install or update the packaged `UEDapBridge` plugin when requested.
4. Build or refresh local metadata when possible.
5. Write or refresh MCP configuration.
6. Report whether setup ended with `ready for agent debugging`.

Do not ask the user to manually start `bin/quilla-mcp.exe` unless MCP
configuration failed and manual diagnosis is needed.

## Read-only Editor Inspection

Goal: confirm Quilla can observe editor state without changing the project.

Order:

1. Check Quilla status.
2. Ping the Unreal Editor bridge.
3. Read editor or PIE world snapshot.
4. List relevant assets only when the next step needs an exact asset path.

Success evidence:

- Bridge responds.
- The target project or editor process is the one the user intended.
- World or asset summaries are returned without saving or mutating content.

## Blueprint Breakpoint Diagnosis

Goal: explain a Blueprint breakpoint hit and identify useful next evidence.

Order:

1. Confirm bridge status.
2. Set or verify the requested Blueprint breakpoint only after the user has
   provided the target asset and node id.
3. Start the demo or PIE path requested by the user.
4. Capture the breakpoint result.
5. Read Blueprint stack and locals.
6. Capture world snapshot and screenshot if the workflow asks for an evidence
   bundle.

Recovery:

- `invalid_blueprint_path`: list assets and copy the exact `/Game/...` path.
- `invalid_node_id`: recreate or inspect the fixture and use the current node
  GUID.
- `breakpoint_hit_timeout`: confirm the node executes during PIE or increase the
  timeout for slow projects.

## C++ And Metadata Inspection

Goal: use Quilla metadata without pretending it contains live memory values.

Order:

1. Check catalog or metadata status.
2. Refresh metadata when it is missing, stale, or uncertain.
3. Inspect classes, properties, callers, or Blueprint-related metadata.
4. Use the user's native debugger for concrete C++ stack frames and memory
   values when needed.

Report ownership clearly:

- Quilla metadata describes Unreal symbols, reflection, and project structure.
- Native debuggers own concrete C++ memory values and physical native stacks.
- UEDapBridge owns Blueprint/editor runtime state surfaced through Quilla.

## Mutating Actions

Ask for user confirmation before actions that can change editor state or project
files, including:

- Starting or stopping PIE.
- Installing or removing breakpoints.
- Saving packages.
- Running arbitrary editor or console commands.
- Creating smoke fixtures.
- Moving actors or changing level state.

Good confirmation text names the command, target project, target asset or actor,
and expected side effect.

## Demo Evidence Summary

After a successful demo, summarize:

- Whether setup reported `ready for agent debugging`.
- Blueprint breakpoint status.
- Stack and local variable evidence.
- World snapshot status.
- Screenshot path.
- JSON artifact path.
- Quilla version, Unreal Engine version, and package or plugin hash if present.
