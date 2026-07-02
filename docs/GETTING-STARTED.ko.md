# Getting Started

[English](GETTING-STARTED.md)

이 문서는 다운로드한 Quilla Windows 패키지에서 로컬 에이전트 디버깅 setup까지
가는 가장 짧은 공개 경로를 설명합니다.

Quilla는 MCP 지원 코딩 에이전트가 실행하는 것을 기본 흐름으로 합니다. 대부분의
사용자는 `bin/quilla-mcp.exe`를 직접 시작할 필요가 없습니다.

한 화면짜리 수동 setup 및 smoke demo 경로가 필요하면
[10분 Quickstart](QUICKSTART-10MIN.ko.md)를 사용하세요.

## 사전 준비

- Windows x64.
- Unreal Editor 5.5 설치.
- 로컬 Unreal 프로젝트. 평가 시에는 source control 아래의 프로젝트나 복사본을
  권장합니다.
- `C:\Tools\Quilla` 같은 안정적인 로컬 폴더에 압축 해제된 Quilla 릴리스
  패키지.
- 로컬 도구 실행과 MCP 설정 업데이트가 가능한 MCP 지원 코딩 에이전트.

Unreal Engine 5.4 호환성은 5.4 설치 환경에서 다시 검증하기 전까지 이번
릴리스의 보장 범위로 말하지 않습니다.

## 패키지 구조

패키지 폴더들은 함께 유지해야 합니다. first-run 도구는 다음 구조를 기대합니다.

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

## 에이전트 중심 setup

대상 Unreal 프로젝트 workspace를 코딩 에이전트에서 연 뒤, 에이전트에게 다음
프롬프트를 줍니다.

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

setup이 성공하면 다음 상태를 보고해야 합니다.

```text
ready for agent debugging
```

first-run 흐름은 패키지 폴더 안의 `artifacts/first-run/` 아래에 로컬 setup
근거를 남길 수 있습니다.

## 수동 fallback

에이전트 연결 전에 Quilla를 평가하는 경우, 패키지 루트에서 다음 명령을 실행할
수 있습니다.

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\YourProject\YourProject.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

setup이 성공한 뒤 에이전트가 configuration refresh를 요구하면 MCP 지원
에이전트를 다시 시작하거나 설정을 다시 읽게 하세요. 생성된 MCP 설정은
`bin/quilla-mcp.exe`를 가리킵니다.

## 데모 실행

setup이 성공한 뒤 에이전트에게 다음처럼 요청할 수 있습니다.

```text
Use my Quilla package to run the included agent debugging demo against my Unreal
Engine project. Capture the Blueprint breakpoint result, stack, locals, world
snapshot, screenshot, and JSON artifact, then summarize what evidence was
captured.

Quilla package path: C:\Tools\Quilla
UE project path: C:\Path\To\YourProject\YourProject.uproject
```

예상되는 데모 근거는 다음과 같습니다.

- Blueprint breakpoint 결과.
- Blueprint stack 및 local 요약.
- Editor 또는 PIE world snapshot.
- Screenshot path.
- Quilla version, Unreal Engine version, project path, package/plugin provenance를
  담은 JSON artifact.

## 안전 관련 메모

- 평가 시에는 disposable project 또는 프로젝트 복사본부터 시작하세요.
- 패키지를 실행하기 전에 `docs/PACKAGE-MANIFEST.json`을 검토하세요.
- 패키지 저장, actor 변경, PIE 시작, smoke fixture 생성처럼 상태를 바꾸는
  동작은 에이전트에게 먼저 설명하게 하세요.
- 생성된 artifact에는 프로젝트별 정보가 포함될 수 있으므로 실행 후 검토하세요.
