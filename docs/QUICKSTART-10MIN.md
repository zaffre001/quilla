# 10-minute Quickstart

[한국어](QUICKSTART-10MIN.ko.md)

This is the shortest manual path for evaluating Quilla `0.2.0` on Windows with
Unreal Editor 5.5.

## 1. Extract

Extract the Windows release package to a stable folder:

```text
C:\Tools\Quilla
```

Keep `bin/`, `plugin/`, `tools/`, and `docs/` together.

## 2. First Run

From the extracted package root:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\Game\Game.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

Success should report:

```text
ready for agent debugging
```

Evidence is written under:

```text
artifacts\first-run\
```

## 3. Smoke Demo

After Unreal Editor is open for the project:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\agent-debugging-demo.ps1 `
  -Project C:\Path\To\Game\Game.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5" `
  -InstallPlugin `
  -BlueprintPath /Game/UE_DAP/BP_UEDapSmoke.BP_UEDapSmoke `
  -NodeId 00000000-0000-0000-0000-000000000000
```

Success should report:

```text
E2E Blueprint breakpoint smoke succeeded.
```

Evidence is written under:

```text
artifacts\e2e\
```

## 4. What To Check

The smoke artifact should include:

- Quilla version, Unreal Engine version, project path, and package/plugin provenance
- Blueprint breakpoint result
- Blueprint stack and local summaries
- Editor or PIE world snapshot
- Screenshot path
- Agent-readable next actions when something fails

## 5. Common Failures

| Symptom | Next action |
| --- | --- |
| `unreal_engine_missing` | Pass the exact UE 5.5 `-EngineRoot`. |
| `plugin_build_failed` | Confirm RunUAT and Visual Studio build tools are available, or use a package with a prebuilt plugin. |
| `editor_not_running` | Open the target project in Unreal Editor, then rerun setup. |
| `pipe_not_connected` | Restart Unreal Editor after plugin install and confirm UEDapBridge is enabled. |
| `breakpoint_hit_timeout` | Confirm the smoke Blueprint node executes during PIE. |
| `invalid_blueprint_path` | Use the exact `/Game/...` asset path from the project. |
| `invalid_node_id` | Recreate or inspect the smoke fixture and use the current node GUID. |

Keep the JSON artifacts. They are the support handoff for determining whether
the failure came from Quilla, Unreal Editor, the plugin build, the project, or
the local environment.
