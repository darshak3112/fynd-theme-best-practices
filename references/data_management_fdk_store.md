# Data Management (Summary)

Source: Fynd Commerce Themes - Data Management.

## Overview
- `fdk-store` wraps the FDK JS SDK and uses Redux Toolkit.
- Exposes `fpi.store`, `fpi.getters`, and helpers to manage state.
- `fdk-store-gql` provides GraphQL-specific store integration.

## Install
- `npm install https://github.com/gofynd/fdk-store.git#v2.0.2`

## Usage
```js
import FDKStore from 'fdk-store';
const { client: fpi } = new FDKStore({ applicationID, applicationToken, domain, storeInitialData });
```

## Read State
- Snapshot: `fpi.store.getState()`
- React subscription: `useGlobalStore(fpi.getters.SLICE)`

## Custom Values
- Set: `fpi.custom.setValue('key', value)`
- Read: `useGlobalStore(fpi.getters.CUSTOM_VALUE)`

## Observe Store (non-React)
- `fpi.observeStore(getterFn, onChange, equalityFn?)`
- Available in `fdk-store >= 2.0.32` and `fdk-store-gql >= 3.0.20`.
