# NexA fork runner policy

Only `push` and `workflow_dispatch` events in `NexA-LLC/OpenHands` may select
`[self-hosted, macOS, ARM64, buildchan]`, currently buildman6. All pull requests,
including same-repository PRs, retain the existing Blacksmith Linux fallback.
No issue/comment event is eligible for the private runner.

The portable frontend build/coverage and frontend/Python/version lint jobs move
for trusted events. Node 22, Python 3.12, pre-commit 3.7.0, and existing checks
remain. The frontend Node matrix's single value is written as `[22]` to repair
the inherited invalid scalar matrix. Official setup-node/setup-python actions
replace provider-specific setup actions. Python/hooks use a fresh job-temp
venv; TypeScript compilation uses the installed local compiler.

The remaining upstream workflows are intentionally unchanged:

- `py-tests.yml`: explicit Linux/Docker/tmux and Windows test coverage.
- `ghcr-build.yml`: Blacksmith Linux image builds, Docker runtime tests,
  provider build-push actions, and their dependent status/description jobs.
- `integration-runner.yml`: runtime integration/provider assumptions and
  comment-triggered coordination need a dedicated Linux review.
- `openhands-resolver.yml`: issue/comment-triggered agent execution may consume
  untrusted instructions and must not execute on the persistent private Mac.
- `lint-fix.yml`: automatic source commits/pushes remain in the existing provider
  execution path; this PR moves read-only checks only.
- `ui-build.yml` and `npm-publish-ui.yml`: Bun setup/publishing need an isolated
  installer and separate package-release compatibility verification.
- `pypi-release.yml`: upstream Poetry/package publication setup remains unchanged.
- `vscode-extension-build.yml`: packaged extension/release validation retains
  the upstream provider environment; it is not replaced by frontend checks.
- `clean-up.yml`, `stale.yml`, `run-eval.yml`: maintenance/comment/external
  evaluation automation stays on its existing runner and was not dispatched.

Static validation covers actionlint, Bash syntax, and whitespace. No full
application install, integration test, image/package publication, workflow
dispatch, or issue/comment automation was run locally. First trusted Mac CI
remains unverified; revert this PR to restore all previous runner selections.
