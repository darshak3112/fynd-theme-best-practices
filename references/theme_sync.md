# Sync Code Between Themes (Summary)

Source: Fynd Commerce Themes - Sync Code Between Themes.

## Purpose
- Sync code from one theme (source) to another (target) without manual file transfer.
- React themes can only sync with React themes (no cross-framework sync).

## Create Context
1. Run in source theme root:
   `fdk theme context -n <context-name>`
2. Choose to initialize as a theme.
3. Select environment (dev or live), company, sales channel, and target theme.
4. This creates `.fdk/context.json` with syncing configuration.

## Sync
- Run `fdk theme sync` to push code from source to target theme.

## Verify
- Check Partner Dashboard: Sales Channel -> Theme -> Store Design to confirm changes on target theme.
