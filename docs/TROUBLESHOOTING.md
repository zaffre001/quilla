# Troubleshooting

Use this guide when first-run setup, MCP connection, bridge communication, or the
Blueprint smoke demo does not complete.

## Quick Checks

Before digging into an error code, confirm:

- The package was extracted completely and folders were not rearranged.
- `docs/PACKAGE-MANIFEST.json` exists.
- Unreal Editor 5.5 is installed at the `-EngineRoot` path you supplied.
- The `-Project` value points to the exact `.uproject` file.
- The project can open normally in Unreal Editor.
- The package plugin exists under `plugin/UEDapBridge/`.
- Your agent has reloaded its MCP configuration after first-run setup.

## Common Failures

| Symptom or code | Likely area | Next action |
| --- | --- | --- |
| `unreal_engine_missing` | Unreal Engine path | Pass the exact `-EngineRoot`, such as `C:\Program Files\Epic Games\UE_5.5`, or install the supported Unreal version. |
| `project_not_found` | Project path | Pass the full path to the `.uproject` file, not only the project folder. |
| `package_manifest_missing` | Incomplete extraction | Re-extract the release package and confirm `docs/PACKAGE-MANIFEST.json` exists. |
| `package_integrity_failed` | Package verification | Stop evaluation, compare hashes with the published release, and request a fresh package if needed. |
| `plugin_build_failed` | Unreal build tools | Open the generated log, confirm Unreal Build Tool is available, and verify the package includes the built plugin for Windows x64. |
| `editor_not_running` | Editor workflow | Open the target project in Unreal Editor, then rerun first-run setup or the demo. |
| `pipe_not_connected` | Bridge/plugin | Confirm the packaged plugin is installed and enabled for the target project, then restart Unreal Editor. |
| `not_connected` | Quilla session | Ask the agent to run Quilla status and configure the active Unreal Editor process. |
| MCP tool does not appear | Agent configuration | Restart or reload the agent after first-run setup writes MCP configuration. |
| `invalid_blueprint_path` | Project asset | Ask the agent to list Blueprint assets and copy the exact `/Game/...` asset path. |
| `invalid_node_id` | Blueprint graph | Recreate or inspect the smoke fixture and use the current node GUID. |
| `breakpoint_hit_timeout` | Demo execution | Confirm the Blueprint node executes during PIE and increase the hit timeout if the project loads slowly. |
| Missing screenshot | Artifact capture | Check the artifact directory and confirm the editor window was available during the demo. |

## Bridge Recovery

If Unreal Editor was restarted, Live Coding reloaded modules, or the plugin was
installed while the editor was open, use this recovery order:

1. Save any wanted project work yourself.
2. Close Unreal Editor.
3. Reopen the target `.uproject`.
4. Ask the agent to run Quilla bridge/status checks.
5. Rerun the first-run setup only if the agent reports missing package,
   configuration, or plugin state.

## Artifact Locations

Typical local evidence is written under the package folder:

- `artifacts/first-run/` for setup evidence.
- `artifacts/e2e/` for demo JSON and screenshots.

If you report a failure, include:

- Quilla package version.
- Unreal Engine version.
- Target project path, with private path segments redacted if needed.
- First-run or demo JSON artifact.
- Any generated log path mentioned in the failure output.

## When To Stop

Stop and request a fresh package or maintainer help if:

- Package hashes do not match the manifest.
- The manifest claims a different version or platform than the downloaded
  release.
- The package contains private source folders, tests, fixtures, or debug symbols
  that were not intentionally provided.
- The agent wants to run mutating editor commands that are not needed for your
  evaluation.
