# FDK Overview & CLI (Summary)

Source: Fynd Developer Kit (FDK) Overview + CLI Commands + Error Codes.

## FDK Overview
- FDK CLI for creating, developing, testing, syncing, and publishing themes/extensions.
- Extension helper libraries: JS/Java/Python.
- Client libraries: JS/Java/Python/Swift/Kotlin.
- Utility libs: Billing, Extension Bridge (JS).

## CLI Global Options
- `--version`, `--help`, `--debug` (writes `debug.log`).

## Common Theme Commands
- `fdk theme new`, `init`, `context`, `context-list`, `active-context`
- `fdk theme serve` (`--ssr`, `--port`)
- `fdk theme sync`, `pull`, `pull-config`, `package`, `open`

## Extension Commands (high level)
- `extension init`, `preview`, `pull-env`, `launch-url`
- Bindings: `binding init/draft/publish/preview/show-context/clear-context`

## Common Error Codes
- FDK-0003 API_ERROR (network)
- FDK-0004 NOT_KNOWN (use --debug)
- FDK-0005 INVALID_INPUT
- FDK-0006 INVALID_KEYS
- FDK-0008 NO_DEVELOPMENT_COMPANY
- FDK-0010 LARGE_PAYLOAD
- FDK-0012 DOWNGRADE_CLI_VERSION
- FDK-0013 VPN_ISSUE
- FDK-0015 CLOUDFLARE_CONNECTION_ISSUE
