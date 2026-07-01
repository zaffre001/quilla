# Quilla

> AI-powered Unreal Engine debugging through a unified MCP + DAP bridge.

Quilla connects AI coding agents to Unreal Engine runtime debugging. It gives
agents a structured debugging interface that combines standard Debug Adapter
Protocol flows, LLDB-backed C++ state, Unreal-aware source metadata, and
Blueprint runtime context.

This repository hosts Quilla release artifacts and user-facing documentation.
It intentionally does not contain the private source tree.

## What Quilla Does

Quilla is built for agent-driven Unreal Engine debugging rather than
human-first GUI debugging.

- Uses `lldb-dap` for C++ memory, variables, and physical call stacks.
- Builds a semantic catalog from Unreal source declarations such as `UCLASS`,
  `UFUNCTION`, and `UPROPERTY`.
- Provides Unreal-specific custom requests over DAP for structured agent
  responses.
- Bridges Blueprint runtime state through the `UEDapBridge` Unreal plugin.
- Packages the server, native helper tools, plugin build output, and license
  metadata for local use.

## Current Preview Target

The current preview is focused on:

- Windows x64
- Unreal Engine 5.4
- C++ Unreal projects
- Agent-oriented debugging workflows
- Local release package setup

Quilla is not currently a GUI debugger, a standalone IDE, a profiler, or a
crash dump analysis tool.

## Installation

Download the latest Windows package from GitHub Releases, then extract it to a
stable local folder.

Expected release package shape:

```text
quilla-<version>-windows-x64/
  server/             Bundled DAP server runtime
  bin/                Native helper executables
  UEDapBridge/        Packaged Unreal Engine plugin, when included
  LICENSE             Release license
  THIRD-PARTY-NOTICES.txt
  PACKAGE-MANIFEST.json
```

If the package includes `UEDapBridge`, copy it into your Unreal project:

```text
<YourProject>/Plugins/UEDapBridge
```

Then regenerate project files or rebuild your Unreal project so the plugin is
available.

## Quick Start

Build a Quilla catalog for your Unreal project:

```powershell
bin\ue-dap-extract.exe `
  --compile-commands <path-to-compile_commands.json> `
  --scan-root <YourProject>\Source `
  --backend auto `
  --format json `
  --output <YourProject>\.quilla\catalog.json
```

Start the Quilla DAP server:

```powershell
node server\lib\index.cjs
```

Connect your agent or DAP client to the running server according to your local
setup.

## Agent Setup Prompt

```text
Use my Quilla release package and set it up for my Unreal Engine project.
Build the catalog and start the first debugging session.

Quilla package path: <path-to-quilla-release>
UE project path: <path-to-uproject-folder>
UE version: 5.4
Platform: Windows
```

## Release Integrity

Each release package should include `PACKAGE-MANIFEST.json` with file hashes
and build metadata. Use it to verify that the package contents match the
published release.

## Source Policy

This repository is for distribution only. Quilla source code, build scripts,
tests, fixtures, and private Unreal plugin implementation files are not
distributed here.

Receiving a release package does not grant permission to copy, modify, reverse
engineer, redistribute, sublicense, sell, lease, or publish Quilla unless a
separate written agreement says otherwise.

## License

Quilla is proprietary software. Release packages are provided under the
license included with each package.

Third-party components remain under their respective licenses and notices.
Unreal Engine is licensed separately by Epic Games. Quilla is not affiliated
with, endorsed by, or sponsored by Epic Games.
