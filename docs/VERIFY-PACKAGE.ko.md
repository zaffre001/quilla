# 패키지 검증

[English](VERIFY-PACKAGE.md)

Quilla 릴리스 패키지는 평가자가 로컬 디버깅 도구를 실행하기 전에 받은 artifact를
확인할 수 있도록 파일 해시와 build metadata를 포함합니다.

## 확인할 파일

압축 해제된 패키지 루트에서 다음 파일이 있는지 확인합니다.

```text
bin/quilla-mcp.exe
bin/quilla-extractor.exe
plugin/UEDapBridge/UEDapBridge.uplugin
tools/quilla-first-run.ps1
tools/agent-debugging-demo.ps1
docs/README.md
docs/LICENSE.txt
docs/PACKAGE-MANIFEST.json
docs/SECURITY-MODEL.md
```

릴리스 소유자가 명시적으로 제공한 경우가 아니라면 패키지에는 private source
folder, test, fixture, local build directory, debug symbol이 없어야 합니다.

## Manifest 필드

`docs/PACKAGE-MANIFEST.json`을 열고 다음을 확인합니다.

- `name`이 `quilla`인지.
- `version`이 다운로드한 release와 일치하는지.
- `platform`이 `windows-x64`인지.
- Build metadata가 source commit 또는 release provenance를 기록하는지.
- File entry가 SHA-256 hash를 포함하는지.
- Release가 제공하는 경우 package integrity status가 있고 성공 상태인지.

`v0.1.0` 기준 manifest는 다음 값을 보고해야 합니다.

```text
name: quilla
version: 0.1.0
platform: windows-x64
```

## Hash 확인

PowerShell로 파일 hash를 계산할 수 있습니다.

```powershell
Get-FileHash .\bin\quilla-mcp.exe -Algorithm SHA256
```

반환된 hash를 `docs/PACKAGE-MANIFEST.json`의 해당 entry와 비교합니다. 다음처럼
보안상 중요한 파일에 대해 반복 확인할 수 있습니다.

- `bin/quilla-mcp.exe`
- `bin/quilla-extractor.exe`
- packaged `UEDapBridge` binaries
- `tools/quilla-first-run.ps1`
- `tools/agent-debugging-demo.ps1`

## 불일치 처리

Hash, version, platform, package layout이 맞지 않으면:

1. 패키지를 실행하지 않습니다.
2. 원본 archive에서 다시 압축 해제하고 다시 확인합니다.
3. 새 패키지를 다운로드하거나 요청합니다.
4. 불일치가 반복되면 조사를 위해 mismatched archive와 manifest를 보관합니다.

## 재배포

Quilla 릴리스 패키지를 받았다고 해서 Quilla를 재배포, 수정, reverse engineer,
sublicense, 판매, 임대, 공개할 권한이 주어지는 것은 아닙니다. 별도의 서면 계약이
없는 한 패키지에 포함된 license가 지배 조건입니다.
