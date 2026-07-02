# Unreal Engine Version Matrix

[한국어](UE-VERSION-MATRIX.ko.md)

Quilla support is validated per Unreal Engine version. A version is supported
only after the packaged plugin builds, the editor bridge answers, read-only
inspection works, and the Blueprint breakpoint demo produces evidence.

## Current Matrix

| Unreal Engine | Status | Notes |
| --- | --- | --- |
| 5.5 | Supported target | Current Windows x64 release target. |
| 5.4 | Not claimed | Do not treat as compatible until revalidated on a 5.4 install. |
| 5.6 | Pending | Candidate for future validation; not a compatibility promise. |
| 5.7+ | Pending | Requires the same validation gates before support is claimed. |

## Support Criteria

`Supported target` means the release has evidence for:

- Package extraction and manifest verification.
- Packaged `UEDapBridge` plugin availability for Windows x64.
- Unreal Editor bridge health check.
- Read-only editor or world inspection.
- Blueprint breakpoint demo producing JSON evidence and a screenshot.
- Artifact metadata including Quilla version, Unreal Engine version, project
  path, and plugin/package provenance.

`Pending` means Quilla may be evaluated on that version, but the public release
does not claim compatibility.

`Not claimed` means users should expect failures unless the release owner has
provided version-specific validation evidence.

## Evaluation Notes

- Use a disposable project or a project copy when testing a new Unreal Engine
  version.
- Record `Engine/Build/Build.version` from the target Unreal installation.
- Keep the first-run JSON, demo JSON, screenshot, and package manifest together
  as the validation record.
- If a version-specific failure is found, report whether it happened during
  plugin load, bridge connection, metadata extraction, MCP operation, PIE, or
  Blueprint breakpoint capture.
