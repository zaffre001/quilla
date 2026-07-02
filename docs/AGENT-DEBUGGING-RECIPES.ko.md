# 에이전트 디버깅 레시피

[English](AGENT-DEBUGGING-RECIPES.md)

이 레시피는 로컬 Quilla 릴리스 패키지를 사용하는 MCP 지원 코딩 에이전트를 위해
작성되었습니다. 먼저 compact status와 diagnosis call을 사용하고, 실제 수정이나
분석에 필요할 때만 더 큰 artifact를 요청하는 흐름을 권장합니다.

Tool 이름은 agent integration에 따라 조금 다를 수 있습니다. 패키지가 노출하는
가장 가까운 Quilla MCP tool을 사용하세요.

## First-run setup

목표: 에이전트가 Quilla를 실행하고 Unreal Editor와 통신할 수 있도록 프로젝트를
준비합니다.

순서:

1. Package layout과 `docs/PACKAGE-MANIFEST.json`을 확인합니다.
2. 대상 `.uproject`와 Unreal Engine 5.5 root로 `tools/quilla-first-run.ps1`을
   실행합니다.
3. 요청되는 경우 packaged `UEDapBridge` plugin을 설치하거나 업데이트합니다.
4. 가능하면 로컬 metadata를 build 또는 refresh합니다.
5. MCP configuration을 작성하거나 refresh합니다.
6. setup이 `ready for agent debugging`으로 끝났는지 보고합니다.

MCP configuration이 실패해서 수동 진단이 필요한 경우가 아니라면 사용자에게
`bin/quilla-mcp.exe`를 직접 시작하라고 요구하지 마세요.

## Read-only editor inspection

목표: 프로젝트를 바꾸지 않고 Quilla가 editor state를 관찰할 수 있는지 확인합니다.

순서:

1. Quilla status를 확인합니다.
2. Unreal Editor bridge를 ping합니다.
3. Editor 또는 PIE world snapshot을 읽습니다.
4. 다음 단계에 정확한 asset path가 필요할 때만 관련 asset을 list합니다.

성공 근거:

- Bridge가 응답합니다.
- 대상 project 또는 editor process가 사용자가 의도한 것입니다.
- World 또는 asset summary가 package save나 content mutation 없이 반환됩니다.

## Blueprint breakpoint diagnosis

목표: Blueprint breakpoint hit을 설명하고 다음 근거를 식별합니다.

순서:

1. Bridge status를 확인합니다.
2. 사용자가 target asset과 node id를 제공한 뒤에만 요청된 Blueprint breakpoint를
   설정하거나 검증합니다.
3. 사용자가 요청한 demo 또는 PIE path를 시작합니다.
4. Breakpoint result를 capture합니다.
5. Blueprint stack과 locals를 읽습니다.
6. workflow가 evidence bundle을 요구하면 world snapshot과 screenshot을
   capture합니다.

복구:

- `invalid_blueprint_path`: asset을 list하고 정확한 `/Game/...` path를 복사합니다.
- `invalid_node_id`: fixture를 다시 만들거나 inspect해서 현재 node GUID를
  사용합니다.
- `breakpoint_hit_timeout`: 해당 node가 PIE 중 실행되는지 확인하거나 느린 프로젝트를
  위해 timeout을 늘립니다.

## C++ 및 metadata inspection

목표: Quilla metadata를 사용하되 live memory value가 들어 있는 것처럼 말하지
않습니다.

순서:

1. Catalog 또는 metadata status를 확인합니다.
2. Metadata가 없거나 stale/uncertain 상태면 refresh합니다.
3. Class, property, caller, Blueprint 관련 metadata를 inspect합니다.
4. 실제 C++ stack frame과 memory value가 필요하면 사용자의 native debugger를
   사용합니다.

Ownership은 명확히 보고합니다.

- Quilla metadata는 Unreal symbol, reflection, project structure를 설명합니다.
- Native debugger는 concrete C++ memory value와 physical native stack을
  소유합니다.
- UEDapBridge는 Quilla를 통해 노출되는 Blueprint/editor runtime state를
  소유합니다.

## Mutating action

다음처럼 editor state나 project file을 바꿀 수 있는 동작은 사용자 확인을 먼저
받습니다.

- PIE 시작 또는 중지.
- Breakpoint 설치 또는 제거.
- Package 저장.
- 임의 editor 또는 console command 실행.
- Smoke fixture 생성.
- Actor 이동 또는 level state 변경.

좋은 확인 문구는 command, target project, target asset 또는 actor, 예상 side
effect를 함께 말합니다.

## Demo evidence 요약

데모가 성공하면 다음을 요약합니다.

- Setup이 `ready for agent debugging`을 보고했는지.
- Blueprint breakpoint status.
- Stack 및 local variable evidence.
- World snapshot status.
- Screenshot path.
- JSON artifact path.
- 존재하는 경우 Quilla version, Unreal Engine version, package 또는 plugin hash.
