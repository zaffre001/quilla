# 지원

[English](SUPPORT.md)

Quilla 0.2.0 Windows 파일럿의 지원 범위는 의도적으로 좁게 유지합니다.

## 포함

- Windows x64 및 Unreal Engine 5.5 first-run setup 지원.
- `quilla-first-run.json` 및 Blueprint smoke artifact 해석 지원.
- Package verification, manifest hash, unsigned binary 상태 확인 안내.
- 유료 파일럿 구매자에게 설치 또는 smoke-test 후속 지원 1회.

## 제외

- Quilla setup과 무관한 일반 Unreal 프로젝트 디버깅.
- 별도 합의가 없는 Unreal Engine 5.5 외 버전.
- 프로젝트 빌드 오류, Blueprint 제작 작업, 게임별 버그 수정.
- GUI debugger, profiler, crash dump analyzer, IDE 지원.

## 지원 채널

Setup 및 package verification 질문은 공개 저장소 issue tracker를 사용하세요.
비공개 프로젝트 소스, private asset, secret, license key, 미공개 게임 콘텐츠는
올리지 마세요. 대신 익명 처리된 first-run 또는 smoke JSON 일부를 공유하세요.

유료 파일럿은 구매 또는 전달 thread에 다음 정보를 포함해 답장하세요.

- Quilla package version 및 zip SHA-256.
- Unreal Engine version.
- First-run result JSON.
- 가능한 경우 Blueprint smoke artifact JSON.
- 기대한 결과와 실제 결과의 짧은 설명.

## 환불 기준

Windows UE 5.5 프로젝트에서 Quilla 제품 문제로 first-run setup이 실패하고
7일 안에 해결하지 못하면 환불 대상입니다. 지원하지 않는 Unreal 버전, 손상된
로컬 Unreal 설치, 프로젝트 빌드 오류, 잘못된 Blueprint path/node 입력은 지원
대상이지만 자동 환불 사유는 아닙니다.
