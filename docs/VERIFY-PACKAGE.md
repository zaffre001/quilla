# Verify Package

[한국어](VERIFY-PACKAGE.ko.md)

Quilla release packages include file hashes and build metadata so evaluators can
confirm which artifact they received before running local debugging tools.

## Files To Check

From the extracted package root, confirm these files exist:

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

The package should not contain private source folders, tests, fixtures, local
build directories, or debug symbols unless the release owner explicitly provided
them.

The customer MCP runtime is `bin/quilla-mcp.exe`. Private TypeScript server
source and built `server/lib` outputs are development references and should not
be present in the public release package.

## Manifest Fields

Open `docs/PACKAGE-MANIFEST.json` and check:

- `name` is `quilla`.
- `version` matches the release you downloaded.
- `platform` is `windows-x64`.
- Build metadata records the source commit or release provenance.
- File entries include SHA-256 hashes.
- Package integrity status is present and successful, when supplied by the
  release.

For `v0.2.0`, the manifest should report:

```text
name: quilla
version: 0.2.0
platform: windows-x64
```

## Hash Check

Use PowerShell to calculate a file hash:

```powershell
Get-FileHash .\bin\quilla-mcp.exe -Algorithm SHA256
```

Compare the returned hash with the matching entry in
`docs/PACKAGE-MANIFEST.json`. Repeat for security-sensitive files such as:

- `bin/quilla-mcp.exe`
- `bin/quilla-extractor.exe`
- packaged `UEDapBridge` binaries
- `tools/quilla-first-run.ps1`
- `tools/agent-debugging-demo.ps1`

## Code Signing Status

Quilla `0.2.0` Windows binaries are not code-signed. This is expected for the
current release. Use the package manifest, GitHub release provenance, and
SHA-256 hashes as the trust check.

PowerShell check:

```powershell
Get-AuthenticodeSignature .\bin\quilla-mcp.exe
Get-AuthenticodeSignature .\bin\quilla-extractor.exe
Get-AuthenticodeSignature .\plugin\UEDapBridge\Binaries\Win64\UnrealEditor-UEDapBridge.dll
```

Expected status for `0.2.0`:

```text
NotSigned
```

## Handling Mismatches

If a hash, version, platform, or package layout does not match:

1. Do not run the package.
2. Re-extract from the original archive and check again.
3. Download or request a fresh package.
4. Keep the mismatched archive and manifest for investigation if the mismatch
   repeats.

## Redistribution

Receiving a Quilla release package does not grant permission to redistribute,
modify, reverse engineer, sublicense, sell, lease, or publish Quilla unless a
separate written agreement says otherwise. Use the license included with the
package for the controlling terms.
