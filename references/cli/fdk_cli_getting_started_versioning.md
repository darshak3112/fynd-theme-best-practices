# FDK CLI: What/Getting Started/Debugging/Versioning (Summary)

Source: FDK CLI docs + theme getting started + debugging + versioning.

## What is FDK CLI
- Build, preview, sync, and publish themes; also supports extensions.
- Features: hot reload, preview before sync, initialize new themes.

## Getting Started Highlights
- Prereqs: Node (doc mentions Node 15), Git, React basics; Mac needs Rosetta.
- Install: `npm install -g @gofynd/fdk-cli`.
- Login: `fdk login` via partner panel.
- Create theme: `fdk theme new -n <name>`.
- Init existing: `fdk theme init`.
- Serve: `fdk theme serve` (SSR on by default; `--ssr false` for client-only dev).
- Sync: `fdk theme sync`.

## Command Reference (Quick)
- `fdk --help`, `fdk theme`, `fdk theme serve --port <port>`.
- Deprecation: `fdk env` deprecated; use `fdk login --host ...`.

## Debugging
- Verify active environment + context.
- Check browser + terminal errors.
- Error codes: FDK-001..005 (directory, context, API, unknown, invalid options).
- Use `--verbose` / `--debug` to generate `debug.log`.

## Versioning Themes
- Use semantic versioning.
- Child themes can optionally upgrade to new parent versions.
- Upgrade steps: backup, replace files, update deps, test.
- Use `npm version` to bump versions.
