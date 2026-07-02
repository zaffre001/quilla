# 보안 모델

Quilla는 AI 에이전트를 위한 로컬 Unreal Engine 디버깅 브리지입니다. 클라우드
서비스, 텔레메트리 에이전트, 분석 SDK, 원격 코드 실행 서비스가 아닙니다.

이 문서는 공개 평가용입니다. 보안 경계, 데이터 흐름, 권한 모델, 안전한 평가
방법을 설명하되, 비공개 구현 세부사항은 공개하지 않습니다.

## 요약

- Quilla는 개발자 PC에서 로컬로 실행됩니다.
- Quilla는 Unreal Editor와 로컬 프로세스 경계 안에서 통신합니다.
- Quilla의 디버깅 루프는 외부 네트워크 접근을 요구하지 않습니다.
- Quilla에는 텔레메트리, 분석, 자동 업데이트 확인이 없습니다.
- Quilla는 명시적인 에이전트 tool call로 요청된 디버깅 정보만 반환합니다.
- Quilla 릴리스 패키지에는 출처 확인용 메타데이터와 파일 해시가 포함됩니다.
- 읽기 전용 inspection 동작과 Editor 상태를 바꾸는 mutating 동작을 구분합니다.

## 신뢰 경계

주요 보안 경계는 로컬 머신과 디버깅 대상 Unreal 프로젝트입니다.

```text
AI agent / MCP 또는 DAP client
        |
        | local stdio 또는 debugger protocol
        v
Quilla local runtime
        |
        | local bridge channel
        v
UEDapBridge Unreal Editor plugin
        |
        v
Unreal Editor / PIE / project state
```

Quilla의 목적은 에이전트가 로그를 긁거나, 스크린샷을 추측하거나, 불완전한
Editor 출력에 의존하지 않고 Unreal 런타임 상태를 구조화해서 읽을 수 있게 하는
것입니다.

## 네트워크 동작

지원되는 디버깅 루프는 로컬에서 동작합니다.

Quilla는 프로젝트 소스, 에셋, 런타임 상태, stack 데이터, Blueprint 데이터,
스크린샷, manifest, 로그를 Quilla가 운영하는 원격 서비스로 의도적으로 보내지
않습니다.

Quilla 릴리스 패키지는 패키지, Unreal Engine, 대상 프로젝트가 이미 머신에
준비된 상태라면 오프라인 평가 환경에서도 사용할 수 있어야 합니다.

사용자가 선택한 AI 에이전트나 MCP 클라이언트는 자체 네트워크 동작을 가질 수
있습니다. Quilla는 그 클라이언트의 동작을 제어하지 않습니다. 평가팀은 에이전트
또는 클라이언트를 별도로 검토해야 합니다.

## 데이터 흐름

Quilla는 MCP/DAP 클라이언트가 요청했을 때 로컬 Unreal 디버깅 상태를 관찰할 수
있습니다. 일반적으로 반환될 수 있는 데이터는 다음과 같습니다.

- Unreal Editor 프로세스 및 bridge 상태.
- Blueprint breakpoint 상태.
- Blueprint call stack 요약.
- 캡처된 Blueprint frame 및 local variable 요약.
- Editor 또는 PIE world 요약.
- JSON 결과 파일이나 screenshot 같은 디버깅 artifact.
- 구조화된 실패 정보와 다음 행동 제안.

Quilla는 프로젝트를 대량으로 내보내기 위한 도구가 아닙니다. 공개 릴리스 문서는
비공개 구현 프로토콜이나 내부 field layout을 설명하지 않습니다.

## 권한 모델

Quilla tool은 크게 두 부류로 나뉩니다.

읽기 전용 inspection tool은 상태를 관찰하며 일반적인 디버깅에 사용됩니다.

- 프로세스 및 세션 상태.
- bridge health check.
- catalog 기반 inspection.
- Blueprint frame 및 stack inspection.
- world 또는 editor snapshot.
- 디버깅 artifact 수집.

Mutating tool은 editor session, runtime state, project file을 바꿀 수 있습니다.

- PIE 시작 또는 중지.
- breakpoint 설치 또는 제거.
- editor command 실행.
- package 저장.
- smoke-test fixture 생성.
- actor 이동 또는 editor state 변경.

에이전트는 특히 대상이 disposable test project가 아니라 production project일
때, mutating action을 실행하기 전에 사용자에게 표시해야 합니다.

이 권한 모델 섹션이 릴리스 패키지의 공개 read-only 및 mutating tool
가이드입니다.

## 감사 가능성

Quilla 릴리스 패키지는 파일 해시와 build metadata가 포함된
`PACKAGE-MANIFEST.json`을 포함해야 합니다. 이를 통해 평가자는 어떤 패키지를
받았고 어떤 파일이 포함되었는지 확인할 수 있습니다.

디버깅 워크플로는 다음 정보를 담은 로컬 artifact를 만들 수 있습니다.

- Quilla version.
- Unreal Engine version.
- 대상 project path.
- plugin binary hash.
- command status.
- 구조화된 error code.
- 캡처된 debugging evidence.

이 artifact는 에이전트의 디버깅 행동이 보이지 않는 상태로 남지 않고 검토
가능하도록 하기 위한 것입니다.

## 배포 무결성

공개 릴리스 패키지는 비공개 source와 debug material을 제외해야 합니다.

- private server source 없음.
- private extractor source 없음.
- Unreal plugin source 없음.
- test 또는 fixture 없음.
- local build directory 없음.
- 릴리스 소유자가 명시적으로 제공한 경우를 제외하고 debug symbol 없음.

릴리스 패키지는 license, third-party notice, setup guide, security guide,
package manifest를 유지해야 합니다.

## 평가 가이드

보수적으로 평가하려면 다음 절차를 권장합니다.

1. disposable Unreal project 또는 대상 프로젝트의 복사본을 사용합니다.
2. 로컬 머신 또는 격리된 VM에서 Quilla를 실행합니다.
3. 평가 정책상 필요하다면 외부 네트워크 접근을 끕니다.
4. 실행 전에 `PACKAGE-MANIFEST.json`을 확인합니다.
5. 읽기 전용 inspection tool부터 사용합니다.
6. editor 또는 project state를 바꾸는 작업은 사용자 승인을 요구합니다.
7. 디버깅 실행 후 생성된 artifact를 검토합니다.

## 알려진 위험

Quilla는 의도적으로 에이전트에게 로컬 디버깅 상태를 구조화해서 제공합니다. 이
접근은 유용하지만, 평가자는 Quilla를 프로젝트를 관찰하거나 바꿀 수 있는 다른
로컬 개발 도구와 같은 수준으로 다루어야 합니다.

알려진 위험은 다음과 같습니다.

- 선택한 AI 에이전트가 prompt 또는 tool result를 자체 서비스로 보낼 수 있음.
- Mutating tool이 editor state 또는 project file을 변경할 수 있음.
- Screenshot 또는 JSON artifact에 프로젝트별 정보가 포함될 수 있음.
- 패키지나 project plugin 설정이 잘못되면 연결 실패 또는 잘못된 editor process
  대상 지정이 발생할 수 있음.
- 릴리스 패키지는 license가 허용하지 않는 한 재배포하면 안 됨.

Quilla의 보안 모델은 로컬 실행, 명시적 tool call, 패키지 출처 증명, 읽기 전용
동작과 mutating 동작의 분리에 기반합니다.

## 비목표

Quilla는 임의의 AI 에이전트를 격리하는 sandbox가 아닙니다. 회사의 source
control 정책, endpoint security, secret scanning 정책, AI 사용 정책을 대체하지
않습니다.

또한 Quilla는 배포된 binary package에 대한 모든 reverse engineering 시도를
완전히 막는다고 주장하지 않습니다. 그 위험을 줄이기 위해 source tree와 비공개
구현 세부사항은 private으로 유지합니다.
