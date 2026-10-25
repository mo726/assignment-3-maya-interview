# Assignment 3: Testing

No assignment features have been implemented or tested yet.

## Initial inspection

- Branch: `assignment-3-maya-interview`.
- The project contains the Next.js App Router starter page and layout.
- Available scripts: `dev`, `build`, `start`, and `lint`.

## Dependency audit

`npm.cmd audit --json` completed with exit code 1 because it reported vulnerabilities.

- Five high-severity affected packages, all in the ESLint development dependency chain:
  `eslint-config-next -> @next/eslint-plugin-next -> fast-glob -> micromatch -> braces`.
- The underlying advisory is a stack-exhaustion denial of service in `braces` when processing deeply nested patterns: https://github.com/advisories/GHSA-vfj7-8cjw-p6xm.
- These are five affected packages tracing to one underlying advisory, not five separate underlying flaws.
- npm proposes changing `eslint-config-next` from 16.4.2 to 14.2.35, flagged as a major-version change. This downgrade needs compatibility review before use.
- No dependencies were changed and no audit fix was run.
- The initial sandboxed audit could not reach the registry; the report above came from the successful retry with registry access.
