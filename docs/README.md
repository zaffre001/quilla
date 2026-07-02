# Quilla Documentation

This folder contains public documentation for Quilla release packages. It is
written for users, evaluators, and MCP-capable coding agents. It does not
include private source, build scripts, tests, fixtures, or internal security
notes.

## Start Here

- [Getting Started](GETTING-STARTED.md) - first-run setup and demo path.
- [Troubleshooting](TROUBLESHOOTING.md) - common setup and runtime failures.
- [Verify Package](VERIFY-PACKAGE.md) - manifest and package integrity checks.
- [Security Model](SECURITY-MODEL.md) - local data flow, permissions, and safe
  evaluation guidance.

## Reference

- [Unreal Engine Version Matrix](UE-VERSION-MATRIX.md) - supported and pending
  Unreal Engine versions.
- [Agent Debugging Recipes](AGENT-DEBUGGING-RECIPES.md) - recommended MCP tool
  order for common debugging workflows.
- [Korean Security Model](SECURITY-MODEL.ko.md) - Korean copy of the public
  security model.

## Package Files

Release packages should also include:

- `LICENSE.txt`
- `PACKAGE-MANIFEST.json`
- Third-party notices, when applicable to the package contents.

Those files describe the specific package you received and should be reviewed
before running Quilla in a project environment.
