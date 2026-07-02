# 문제 해결

[English](TROUBLESHOOTING.md)

first-run setup, MCP 연결, bridge 통신, Blueprint smoke 데모가 완료되지 않을 때
이 문서를 사용하세요.

## 빠른 확인

error code를 깊게 보기 전에 다음을 확인합니다.

- 패키지가 완전히 압축 해제되었고 폴더 구조가 바뀌지 않았는지.
- `docs/PACKAGE-MANIFEST.json`이 존재하는지.
- Unreal Editor 5.5가 입력한 `-EngineRoot` 경로에 설치되어 있는지.
- `-Project` 값이 프로젝트 폴더가 아니라 정확한 `.uproject` 파일을 가리키는지.
- 해당 프로젝트가 Unreal Editor에서 정상적으로 열리는지.
- 패키지 플러그인이 `plugin/UEDapBridge/` 아래에 있는지.
- first-run setup 뒤 에이전트가 MCP configuration을 다시 읽었는지.

## 일반적인 실패

| 증상 또는 code | 가능성 높은 영역 | 다음 행동 |
| --- | --- | --- |
| `unreal_engine_missing` | Unreal Engine 경로 | `C:\Program Files\Epic Games\UE_5.5` 같은 정확한 `-EngineRoot`를 전달하거나 지원되는 Unreal 버전을 설치합니다. |
| `project_not_found` | Project 경로 | 프로젝트 폴더가 아니라 `.uproject` 파일의 전체 경로를 전달합니다. |
| `package_manifest_missing` | 불완전한 압축 해제 | 릴리스 패키지를 다시 압축 해제하고 `docs/PACKAGE-MANIFEST.json`이 있는지 확인합니다. |
| `package_integrity_failed` | 패키지 검증 | 평가를 멈추고 published release의 hash와 비교한 뒤 필요하면 새 패키지를 요청합니다. |
| `plugin_build_failed` | Unreal build tools | 생성된 log를 열고 Unreal Build Tool 사용 가능 여부와 Windows x64용 built plugin 포함 여부를 확인합니다. |
| `editor_not_running` | Editor workflow | 대상 프로젝트를 Unreal Editor에서 연 뒤 first-run setup 또는 demo를 다시 실행합니다. |
| `pipe_not_connected` | Bridge/plugin | 패키징된 플러그인이 대상 프로젝트에 설치 및 활성화되어 있는지 확인한 뒤 Unreal Editor를 재시작합니다. |
| `not_connected` | Quilla session | 에이전트에게 Quilla status를 확인하고 active Unreal Editor process를 configure하게 합니다. |
| MCP tool이 보이지 않음 | Agent configuration | first-run setup이 MCP configuration을 쓴 뒤 에이전트를 재시작하거나 설정을 다시 읽게 합니다. |
| `invalid_blueprint_path` | Project asset | 에이전트에게 Blueprint asset을 list하게 하고 정확한 `/Game/...` asset path를 복사합니다. |
| `invalid_node_id` | Blueprint graph | smoke fixture를 다시 만들거나 inspect해서 현재 node GUID를 사용합니다. |
| `breakpoint_hit_timeout` | Demo execution | 해당 Blueprint node가 PIE 중 실행되는지 확인하고 프로젝트 로딩이 느리면 timeout을 늘립니다. |
| screenshot 누락 | Artifact capture | artifact directory를 확인하고 demo 중 editor window를 사용할 수 있었는지 확인합니다. |

## Bridge 복구

Unreal Editor를 재시작했거나, Live Coding이 module을 reload했거나, editor가 열린
상태에서 plugin을 설치했다면 다음 순서로 복구합니다.

1. 필요한 프로젝트 작업은 사용자가 직접 저장합니다.
2. Unreal Editor를 닫습니다.
3. 대상 `.uproject`를 다시 엽니다.
4. 에이전트에게 Quilla bridge/status check를 실행하게 합니다.
5. 에이전트가 package, configuration, plugin 상태 누락을 보고할 때만 first-run
   setup을 다시 실행합니다.

## Artifact 위치

일반적인 로컬 근거는 패키지 폴더 아래에 작성됩니다.

- `artifacts/first-run/` - setup 근거.
- `artifacts/e2e/` - demo JSON 및 screenshot.

실패를 보고할 때는 다음을 포함하세요.

- Quilla package version.
- Unreal Engine version.
- 대상 project path. 필요하면 민감한 path segment는 가립니다.
- first-run 또는 demo JSON artifact.
- 실패 출력에 언급된 generated log path.

## 멈춰야 하는 경우

다음 경우에는 실행을 멈추고 새 패키지 또는 maintainer 도움을 요청하세요.

- Package hash가 manifest와 맞지 않음.
- Manifest의 version 또는 platform이 다운로드한 release와 다름.
- 패키지에 의도적으로 제공되지 않은 private source folder, test, fixture, debug
  symbol이 들어 있음.
- 평가에 필요하지 않은 mutating editor command를 에이전트가 실행하려고 함.
