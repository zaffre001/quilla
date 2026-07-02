# Quilla

Quilla는 AI 코딩 에이전트를 위한 로컬 Unreal Engine 디버깅 브리지입니다.

Rust native MCP 런타임, native Unreal metadata extractor, 패키징된 Unreal
Editor 브리지 플러그인을 묶은 Windows 패키지로 배포됩니다. 목표는 단순합니다.
에이전트가 로그, 스크린샷, 터미널 출력만 보고 추측하지 않고 구조화된 Unreal
디버깅 상태를 읽을 수 있게 하는 것입니다.

이 저장소는 Quilla 릴리스 artifact와 사용자용 문서를 위한 공개 배포
저장소입니다. 비공개 소스 트리는 포함하지 않습니다.

[English README](README.md)

## 현재 릴리스

현재 릴리스 대상은 다음과 같습니다.

- Quilla `0.2.0`
- Windows x64
- Unreal Editor `5.5`
- MCP 지원 코딩 에이전트
- 로컬 Unreal Editor 디버깅 워크플로
- C++ 및 Blueprint 디버깅 context

Unreal Engine 5.4 호환성은 5.4 설치 환경에서 다시 검증하기 전까지 이번
릴리스의 보장 범위로 말하지 않습니다.

## 제공 기능

- `bin/quilla-mcp.exe`: 에이전트 디버깅을 위한 Rust native MCP 런타임.
- `bin/quilla-extractor.exe`: Unreal C++ metadata를 추출하는 native 도구.
- 패키징된 `UEDapBridge` Unreal Editor 플러그인.
- 에이전트가 읽을 수 있는 Unreal Editor, PIE, C++, Blueprint 디버깅 context.
- 로컬 브리지를 통한 Blueprint breakpoint 워크플로.
- 에이전트 근거로 사용할 stack, locals, world snapshot, screenshot, JSON artifact.
- 불투명한 실패 대신 다음 행동을 포함한 구조화된 실패 응답.
- `docs/PACKAGE-MANIFEST.json`에 포함되는 SHA-256 해시 기반 릴리스 메타데이터.

Quilla는 GUI 디버거, standalone IDE, profiler, crash dump analyzer, static
analyzer가 아닙니다.

## 패키지 구조

Windows 릴리스 패키지는 다음 형태입니다.

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

패키지 폴더들은 함께 유지해야 합니다. first-run 도구는 이 구조를 기준으로
플러그인을 설치하고, 로컬 metadata를 만들고, MCP 설정을 작성합니다.

## 에이전트 중심 설치

권장 설치 방식은 MCP 지원 에이전트에게 first-run setup을 맡기는 것입니다.
사용자가 직접 `quilla-mcp.exe`를 실행하는 흐름이 아닙니다. first-run 도구가
지원되는 에이전트용 MCP 설정을 작성하고, 이후 에이전트가 그 설정을 통해
런타임을 실행합니다.

1. GitHub Releases에서 최신 Windows 패키지를 다운로드합니다.
2. `C:\Tools\Quilla` 같은 안정적인 로컬 폴더에 압축 해제합니다.
3. 사용 중인 에이전트에서 Unreal 프로젝트 workspace를 엽니다.
4. 에이전트에게 다음 프롬프트를 줍니다.

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

설치가 성공하면 다음 상태를 보고해야 합니다.

```text
ready for agent debugging
```

가장 짧은 수동 경로는 [10분 Quickstart](docs/QUICKSTART-10MIN.ko.md)를
사용하세요. first-run 명령, smoke demo 명령, 예상 artifact, 실패별 다음 행동을
한 화면에 정리했습니다.

아직 에이전트를 사용하지 않는 경우의 수동 fallback:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\quilla-first-run.ps1 `
  -Project C:\Path\To\YourProject\YourProject.uproject `
  -EngineRoot "C:\Program Files\Epic Games\UE_5.5"
```

설정이 `ready for agent debugging` 상태로 끝나면, 에이전트가 설정 새로고침을
요구하는 경우 에이전트를 다시 시작하거나 MCP 설정을 다시 읽게 하세요. 그 다음
에이전트에게 Quilla 데모를 실행하거나 Quilla 도구를 사용하라고 요청하면 됩니다.
생성된 MCP 설정이 `bin/quilla-mcp.exe`를 가리키므로 사용자가 서버를 직접 실행할
필요는 없습니다.

## 에이전트 디버깅 데모

릴리스 패키지에는 반복 가능한 디버깅 데모가 포함됩니다. 에이전트에게 이렇게
요청할 수 있습니다.

```text
Use my Quilla package to run the included agent debugging demo against my Unreal
Engine project. Capture the Blueprint breakpoint result, stack, locals, world
snapshot, screenshot, and JSON artifact, then summarize what evidence was
captured.

Quilla package path: C:\Tools\Quilla
UE project path: C:\Path\To\YourProject\YourProject.uproject
```

이 데모는 패키지 파일이 복사됐다는 것만 확인하는 것이 아니라, 설정 후 Quilla가
실제 디버깅 결과를 캡처할 수 있는지 보여주기 위한 것입니다.

## 릴리스 무결성

각 패키지에는 파일 크기, SHA-256 해시, build metadata, artifact를 생성한 source
commit이 포함된 `docs/PACKAGE-MANIFEST.json`이 들어갑니다.
현재 `0.2.0` Windows 패키지는 code-signed 상태가 아닙니다. 다운로드한 GitHub
release artifact와 manifest hash로 검증하세요.

`v0.2.0` 기준 package manifest는 다음 값을 보고해야 합니다.

```text
name: quilla
version: 0.2.0
platform: windows-x64
```

패키지를 설치하거나 내부에 재배포하기 전에 manifest를 확인하세요.

## 문서

- [문서 색인](docs/README.ko.md) - 공개 문서의 한국어 안내.
- [Getting Started](docs/GETTING-STARTED.ko.md) - first-run setup, 성공 출력,
  데모 경로.
- [10분 Quickstart](docs/QUICKSTART-10MIN.ko.md) - 한 화면 setup 및 smoke
  demo 경로.
- [문제 해결](docs/TROUBLESHOOTING.ko.md) - setup, bridge, MCP, Blueprint smoke
  실패 대응.
- [패키지 검증](docs/VERIFY-PACKAGE.ko.md) - package layout과 manifest 확인.
- [Unreal Engine 버전 매트릭스](docs/UE-VERSION-MATRIX.ko.md) - Unreal Engine
  버전별 지원 상태와 검증 기준.
- [에이전트 디버깅 레시피](docs/AGENT-DEBUGGING-RECIPES.ko.md) - 일반적인
  디버깅 작업에서 권장되는 MCP tool 순서.
- [보안 모델](docs/SECURITY-MODEL.ko.md) - 로컬 데이터 흐름, 권한, 안전한
  평가 가이드.
- [지원](docs/SUPPORT.ko.md) - 지원 범위, 채널, 환불 기준.

## 보안

Quilla는 로컬에서 실행되며 프로젝트 데이터를 Quilla가 운영하는 외부 서비스로
전송하지 않습니다. 디버깅 루프는 로컬 에이전트, 로컬 런타임, Unreal Editor
경계 안에서 동작합니다.

데이터 흐름, 권한 모델, 패키지 무결성, 안전한 평가 방법은
[보안 모델](docs/SECURITY-MODEL.ko.md)을 참고하세요. 영어 문서는
[Security Model](docs/SECURITY-MODEL.md)에 있습니다.

## 소스 정책

Quilla는 proprietary software입니다. 이 배포 저장소는 의도적으로 릴리스
artifact와 사용자용 문서만 포함합니다.

공개 런타임은 릴리스 패키지에 포함된 Rust native MCP 서버
`bin/quilla-mcp.exe`입니다. 비공개 TypeScript server source와 빌드된
`server/lib` 출력물은 개발용 reference일 뿐이며, 고객 사용에 필요하지 않고
공개 패키지에 포함된다고 기대해서는 안 됩니다.

Quilla는 원래 source-available 프로젝트로 공개할 계획이었습니다. 하지만 이전
도구들에서 핵심 구현 세부사항이 출처 표시 없이 복제되는 경험을 한 뒤, Quilla는
비공개 소스 기반의 proprietary release package로 배포하게 되었습니다. 양해
부탁드립니다.

릴리스 패키지를 받았다고 해서 Quilla를 복사, 수정, reverse engineer, 재배포,
sublicense, 판매, 임대, 공개할 권한이 주어지는 것은 아닙니다. 별도의 서면
계약이 있는 경우에만 그 계약을 따릅니다.

## 라이선스

Quilla 릴리스 패키지는 각 패키지의 `docs/LICENSE.txt`에 포함된 license에 따라
제공됩니다.

Third-party component는 각자의 license와 notice를 따릅니다. Unreal Engine은
Epic Games가 별도로 라이선스합니다. Quilla는 Epic Games와 제휴, 보증, 후원을
받지 않습니다.
