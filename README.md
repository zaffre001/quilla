# Quilla

Quilla is a local Unreal Engine debugging bridge for AI coding agents.

It ships as a Windows package built around a Rust native MCP runtime, a native
Unreal metadata extractor, and a packaged Unreal Editor bridge plugin. The goal
is simple: give an agent structured Unreal debugging state instead of asking it
to infer everything from logs, screenshots, or terminal text.

This repository is the public distribution home for Quilla release artifacts and
user-facing documentation. It does not contain the private source tree.

[Korean README](README.ko.md)

## Current Release

The current release target is:

- Quilla `0.1.0`
- Windows x64
- Unreal Editor `5.5`
- MCP-capable coding agents
- Local Unreal Editor debugging workflows
- C++ and Blueprint debugging context

Unreal Engine 5.4 compatibility is not claimed for this release until it is
revalidated on a 5.4 install.

## What It Provides

- `bin/quilla-mcp.exe`, a Rust native MCP runtime for agent debugging.
- `bin/quilla-extractor.exe`, a native Unreal C++ metadata extractor.
- A packaged `UEDapBridge` Unreal Editor plugin.
- Agent-readable Unreal Editor, PIE, C++, and Blueprint debugging context.
- Blueprint breakpoint workflows through the local bridge.
- Captured stack, locals, world snapshot, screenshot, and JSON artifacts for
  agent evidence.
- Structured failure responses with next actions instead of opaque errors.
- Release metadata with SHA-256 hashes in `docs/PACKAGE-MANIFEST.json`.

Quilla is not a GUI debugger, standalone IDE, profiler, crash dump analyzer, or
static analyzer.

## Package Layout

A Windows release package is shaped like this:

```text
Quilla/
  bin/
    quilla-mcp.exe
    quilla-extractor.exe
  plugin/
    UEDapBridge/
      Binaries/
      Resources/
      UEDapBridge.uplugin
  tools/
    quilla-first-run.ps1
    agent-debugging-demo.ps1
  docs/
    README.md
    LICENSE.txt
    PACKAGE-MANIFEST.json
    SECURITY-MODEL.md
    GETTING-STARTED.md
    TROUBLESHOOTING.md
    VERIFY-PACKAGE.md
    UE-VERSION-MATRIX.md
    AGENT-DEBUGGING-RECIPES.md
```

Keep the package folders together. The first-run tool expects this layout when
it installs the plugin, builds local metadata, and writes the MCP configuration.

## Agent-first Installation

The recommended setup path is to let an MCP-capable agent perform first-run
setup. Users normally do not start `quilla-mcp.exe` by hand. The first-run tool
writes MCP configuration for supported agents so the agent can launch the
runtime later.

1. Download the latest Windows package from GitHub Releases.
2. Extract it to a stable local folder, such as `C:\Tools\Quilla`.
3. Open your Unreal project workspace in your agent.
4. Give the agent this prompt:

```text
Use my Quilla release package to set up agent debugging for my Unreal Engine
5.5 project.

Run the included first-run setup, install or update the packaged UEDapBridge
plugin for the project, build the local Unreal metadata catalog when possible,
write the MCP configuration so the agent can launch bin/quilla-mcp.exe, ping the
local bridge, and report whether the project is ready for agent debugging.

Quilla package path: C:\Tools\Quilla
UE project path: C:\Path\To\YourProject\YourProject.uproject
UE version: 5.5
Platform: Windows
```

Successful setup should report:

```text
ready for agent debugging
```

Manual fallback, if you are not using an agent yet:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\YourProject\YourProject.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

After setup reports `ready for agent debugging`, reload or restart your
MCP-capable agent if it asks you to refresh its configuration. Then ask the
agent to run the Quilla demo or use Quilla tools. The generated MCP
configuration points the agent at `bin/quilla-mcp.exe`, so you do not need to
start the server yourself.

## Agent Debugging Demo

The release package includes a repeatable debugging demo. Ask the agent:

```text
Use my Quilla package to run the included agent debugging demo against my Unreal
Engine project. Capture the Blueprint breakpoint result, stack, locals, world
snapshot, screenshot, and JSON artifact, then summarize what evidence was
captured.

Quilla package path: C:\Tools\Quilla
UE project path: C:\Path\To\YourProject\YourProject.uproject
```

The demo is intended to show that Quilla can capture a real debugging result
after setup, not just that package files were copied successfully.

## Release Integrity

Each package includes `docs/PACKAGE-MANIFEST.json` with file sizes, SHA-256
hashes, build metadata, and the source commit used to generate the artifact.

For `v0.1.0`, the package manifest should report:

```text
name: quilla
version: 0.1.0
platform: windows-x64
```

Use the manifest before installing or redistributing a package internally.

## Documentation

- [Korean Documentation](docs/README.ko.md) - Korean map of public documents.
- [Documentation Index](docs/README.md) - short map of public documents.
- [Getting Started](docs/GETTING-STARTED.md) - first-run setup, expected
  success output, and the demo path.
- [Troubleshooting](docs/TROUBLESHOOTING.md) - common setup, bridge, MCP, and
  Blueprint smoke failures.
- [Verify Package](docs/VERIFY-PACKAGE.md) - package layout and manifest checks.
- [Unreal Engine Version Matrix](docs/UE-VERSION-MATRIX.md) - support status and
  validation criteria by Unreal Engine version.
- [Agent Debugging Recipes](docs/AGENT-DEBUGGING-RECIPES.md) - recommended MCP
  tool order for common debugging tasks.
- [Security Model](docs/SECURITY-MODEL.md) - local data flow, permissions, and
  safe evaluation guidance.

## Security

Quilla runs locally and does not send project data to Quilla-operated external
services. The debugging loop uses local agent, local runtime, and Unreal Editor
boundaries.

See [Security Model](docs/SECURITY-MODEL.md) for data flow, permission model,
package integrity, and safe evaluation guidance. Korean readers can use
[Korean Security Model](docs/SECURITY-MODEL.ko.md).

## Source Policy

Quilla is proprietary software. This distribution repository is intentionally
limited to release artifacts and user-facing documentation.

Quilla was originally planned as a source-available project. After prior
experiences where key implementation details from earlier tools were copied
without attribution, Quilla is now distributed as proprietary release packages
instead. Thank you for understanding.

Receiving a release package does not grant permission to copy, modify, reverse
engineer, redistribute, sublicense, sell, lease, or publish Quilla unless a
separate written agreement says otherwise.

## License

Quilla release packages are provided under the license included with each
package at `docs/LICENSE.txt`.

Third-party components remain under their respective licenses and notices.
Unreal Engine is licensed separately by Epic Games. Quilla is not affiliated
with, endorsed by, or sponsored by Epic Games.
