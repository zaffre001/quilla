# Quilla 문서

[English documentation](README.md)

이 폴더는 Quilla 릴리스 패키지를 위한 공개 문서를 담습니다. 사용자, 평가자,
MCP 지원 코딩 에이전트를 대상으로 작성되어 있으며, 비공개 소스, 빌드 스크립트,
테스트, fixture, 내부 보안 메모는 포함하지 않습니다.

## 먼저 읽을 문서

- [10분 Quickstart](QUICKSTART-10MIN.ko.md) - 가장 짧은 수동 setup 및 smoke
  demo 경로.
- [Getting Started](GETTING-STARTED.ko.md) - 에이전트 중심 setup과 데모 경로.
- [문제 해결](TROUBLESHOOTING.ko.md) - 일반적인 setup 및 runtime 실패.
- [패키지 검증](VERIFY-PACKAGE.ko.md) - manifest와 package integrity 확인.
- [보안 모델](SECURITY-MODEL.ko.md) - 로컬 데이터 흐름, 권한, 안전한 평가
  가이드.
- [지원](SUPPORT.ko.md) - 지원 범위, 채널, 환불 기준.

## 참고 문서

- [Unreal Engine 버전 매트릭스](UE-VERSION-MATRIX.ko.md) - 지원 및 검증 예정
  Unreal Engine 버전.
- [에이전트 디버깅 레시피](AGENT-DEBUGGING-RECIPES.ko.md) - 일반적인 디버깅
  워크플로에서 권장되는 MCP tool 순서.
- [Sample Blueprint Smoke JSON](SAMPLE-BLUEPRINT-SMOKE.json) - 익명 처리된
  success artifact 형태.
- [Security Model](SECURITY-MODEL.md) - 공개 보안 모델의 영어 문서.

## 패키지 파일

릴리스 패키지에는 보통 다음 파일도 포함됩니다.

- `LICENSE.txt`
- `PACKAGE-MANIFEST.json`
- 패키지 구성에 따라 third-party notices

이 파일들은 받은 패키지의 정확한 조건과 무결성을 설명합니다. 프로젝트 환경에서
Quilla를 실행하기 전에 확인하는 것을 권장합니다.
