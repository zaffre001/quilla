# 10분 Quickstart

[English](QUICKSTART-10MIN.md)

Windows + Unreal Editor 5.5에서 Quilla `0.1.1`을 가장 짧게 평가하는 수동
경로입니다.

## 1. 압축 해제

Windows 릴리스 패키지를 안정적인 폴더에 압축 해제합니다.

```text
C:\Tools\Quilla
```

`bin/`, `plugin/`, `tools/`, `docs/` 폴더를 함께 유지하세요.

## 2. First Run

압축 해제한 패키지 루트에서 실행합니다.

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\Game\Game.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

성공 시 다음 상태가 보여야 합니다.

```text
ready for agent debugging
```

근거 파일은 아래에 기록됩니다.

```text
artifacts\first-run\
```

## 3. Smoke Demo

대상 프로젝트가 Unreal Editor에서 열린 뒤 실행합니다.

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\agent-debugging-demo.ps1 `
  -Project C:\Path\To\Game\Game.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5" `
  -InstallPlugin `
  -BlueprintPath /Game/UE_DAP/BP_UEDapSmoke.BP_UEDapSmoke `
  -NodeId 00000000-0000-0000-0000-000000000000
```

성공 시 다음 문구가 보여야 합니다.

```text
E2E Blueprint breakpoint smoke succeeded.
```

근거 파일은 아래에 기록됩니다.

```text
artifacts\e2e\
```

## 4. 확인할 것

Smoke artifact에는 다음이 포함되어야 합니다.

- Quilla version, Unreal Engine version, project path, package/plugin provenance
- Blueprint breakpoint result
- Blueprint stack 및 local summary
- Editor 또는 PIE world snapshot
- Screenshot path
- 실패 시 에이전트가 읽을 수 있는 next action

## 5. 흔한 실패

| 증상 | 다음 행동 |
| --- | --- |
| `unreal_engine_missing` | 정확한 UE 5.5 `-EngineRoot`를 넘기세요. |
| `plugin_build_failed` | RunUAT와 Visual Studio build tools가 있는지 확인하거나 prebuilt plugin이 포함된 패키지를 사용하세요. |
| `editor_not_running` | 대상 프로젝트를 Unreal Editor에서 연 뒤 setup을 다시 실행하세요. |
| `pipe_not_connected` | Plugin 설치 후 Unreal Editor를 다시 시작하고 UEDapBridge가 활성화됐는지 확인하세요. |
| `breakpoint_hit_timeout` | Smoke Blueprint node가 PIE 중 실행되는지 확인하세요. |
| `invalid_blueprint_path` | 프로젝트의 정확한 `/Game/...` asset path를 사용하세요. |
| `invalid_node_id` | Smoke fixture를 다시 만들거나 검사해서 현재 node GUID를 사용하세요. |

JSON artifact를 보관하세요. 실패가 Quilla, Unreal Editor, plugin build, 프로젝트,
로컬 환경 중 어디에서 났는지 판단하는 support handoff입니다.
