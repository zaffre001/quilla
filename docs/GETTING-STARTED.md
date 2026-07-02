# Getting Started

[한국어](GETTING-STARTED.ko.md)

This guide is the shortest public path from a downloaded Quilla Windows package
to a working local agent debugging setup.

Quilla is intended to be launched by an MCP-capable coding agent. Most users do
not need to start `bin/quilla-mcp.exe` by hand.

## Prerequisites

- Windows x64.
- Unreal Editor 5.5 installed.
- A local Unreal project, preferably under source control or copied for
  evaluation.
- A Quilla release package extracted to a stable local folder, such as
  `C:\Tools\Quilla`.
- An MCP-capable coding agent that can run local tools and update its MCP
  configuration.

Unreal Engine 5.4 compatibility is not claimed for this release until it is
revalidated on a 5.4 install.

## Package Layout

Keep the package folders together. The first-run tool expects this shape:

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
```

## Agent-first Setup

Open the target Unreal project workspace in your coding agent, then give it this
prompt:

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

The first-run flow may write local setup evidence under
`artifacts/first-run/` inside the package folder.

## Manual Fallback

If you are evaluating Quilla before connecting it to an agent, run this from the
package root:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\YourProject\YourProject.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

After a successful setup, reload or restart the MCP-capable agent if it asks you
to refresh configuration. The generated MCP configuration points the agent at
`bin/quilla-mcp.exe`.

## Run The Demo

After setup succeeds, ask the agent:

```text
Use my Quilla package to run the included agent debugging demo against my Unreal
Engine project. Capture the Blueprint breakpoint result, stack, locals, world
snapshot, screenshot, and JSON artifact, then summarize what evidence was
captured.

Quilla package path: C:\Tools\Quilla
UE project path: C:\Path\To\YourProject\YourProject.uproject
```

Expected demo evidence includes:

- Blueprint breakpoint result.
- Blueprint stack and local summaries.
- Editor or PIE world snapshot.
- Screenshot path.
- JSON artifact with Quilla version, Unreal Engine version, project path, and
  package/plugin provenance.

## Safety Notes

- Start with a disposable project or a copy of the project for evaluation.
- Review `docs/PACKAGE-MANIFEST.json` before running the package.
- Ask the agent to explain mutating actions before it saves packages, changes
  actors, starts PIE, or creates smoke fixtures.
- Review generated artifacts after the run because they may contain
  project-specific information.
