# Unreal Engine 버전 매트릭스

[English](UE-VERSION-MATRIX.md)

Quilla 지원은 Unreal Engine 버전별로 검증됩니다. 어떤 버전이 지원된다고 말하려면
패키징된 plugin build, editor bridge 응답, read-only inspection, Blueprint
breakpoint demo evidence가 모두 확인되어야 합니다.

## 현재 매트릭스

| Unreal Engine | 상태 | 메모 |
| --- | --- | --- |
| 5.5 | 지원 대상 | 현재 Windows x64 릴리스 대상입니다. |
| 5.4 | 보장하지 않음 | 5.4 설치 환경에서 다시 검증하기 전까지 호환으로 취급하지 않습니다. |
| 5.6 | 검증 예정 | 향후 검증 후보이며 compatibility promise가 아닙니다. |
| 5.7+ | 검증 예정 | 지원을 주장하려면 동일한 validation gate가 필요합니다. |

## 지원 기준

`지원 대상`은 릴리스가 다음 근거를 가진다는 뜻입니다.

- Package extraction 및 manifest verification.
- Windows x64용 packaged `UEDapBridge` plugin availability.
- Unreal Editor bridge health check.
- Read-only editor 또는 world inspection.
- JSON evidence와 screenshot을 생성하는 Blueprint breakpoint demo.
- Quilla version, Unreal Engine version, project path, plugin/package
  provenance를 포함한 artifact metadata.

`검증 예정`은 해당 버전에서 평가할 수는 있지만 공개 릴리스가 compatibility를
주장하지 않는다는 뜻입니다.

`보장하지 않음`은 릴리스 소유자가 버전별 validation evidence를 제공하지 않는 한
실패를 예상해야 한다는 뜻입니다.

## 평가 메모

- 새 Unreal Engine 버전을 테스트할 때는 disposable project 또는 project copy를
  사용하세요.
- 대상 Unreal 설치의 `Engine/Build/Build.version`을 기록하세요.
- First-run JSON, demo JSON, screenshot, package manifest를 함께 validation
  record로 보관하세요.
- 버전별 실패를 발견하면 plugin load, bridge connection, metadata extraction,
  MCP operation, PIE, Blueprint breakpoint capture 중 어디에서 발생했는지
  보고하세요.
