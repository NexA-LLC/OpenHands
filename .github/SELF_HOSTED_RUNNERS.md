# NexA fork runner policy

Public self-hosted execution is prepared but off by default. The buildman6
Default runner group currently excludes public repositories. Enabling it
requires both public-repository runner access and the repository/organization
variable `NEXA_PUBLIC_SELF_HOSTED_RUNNER_ENABLED` set to `true`. This PR changes
neither setting. With the variable unset or false, jobs keep the original
Blacksmith Linux runner. The existing trusted-event checks still apply after
opt-in; enabling the variable never grants fork PRs access to the private Mac.

Only `push` and `workflow_dispatch` events in `NexA-LLC/OpenHands` may select
`[self-hosted, macOS, ARM64, buildchan]`, currently buildman6. All pull requests,
including same-repository PRs, retain the existing Blacksmith Linux fallback.
No issue/comment event is eligible for the private runner.

The portable frontend build/coverage and frontend/Python/version lint jobs can
move for trusted events after opt-in. Node 22, Python 3.12, pre-commit 3.7.0, and existing checks
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

Mac Python provisioning uses checksum-verified uv 0.13.1 from the pinned v7
action instead of hosted-image Python installers that assume `/Users/runner`
or install system frameworks. The original Python version specifications and
matrices remain. Managed interpreters, seeded pip, venvs, Python bin links,
download caches, and Poetry configuration stay under `RUNNER_TEMP`; no global
Python or shell profile is modified. Each job asserts its requested Python
version and temporary interpreter/base/pip prefixes before running tests.

On the actual buildman6 ARM64 host, Python 3.11.17, 3.12.15, 3.13.16, and 3.14.8
were provisioned for the declared 3.11–3.14 specifications, with interpreter,
base prefix and pip paths verified inside a disposable temporary root.
The full application suites still require current-head CI confirmation.

All PR/Linux fallback runners retain the original setup-python step; managed
uv provisioning is conditional on the trusted Mac OS/architecture selection.
The fallback venv may symlink its Python executable to that hosted interpreter;
its venv and pip must still be temporary. Resolving the executable and base
prefix into temporary storage is required only for managed Mac Python. Both
fallback environment steps passed with a real stdlib venv and an external
interpreter symlink in an offline probe.
