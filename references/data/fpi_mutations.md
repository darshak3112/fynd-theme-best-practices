# FPI Mutation Functions (Summary)

Source: Fynd Commerce Themes - FPI Mutation Functions.

## Purpose
- Extensions can intercept/modify GraphQL queries & mutations triggered by the theme.

## API
```js
fpi.mutations.apply(operationName, callback)
fpi.mutations.remove(operationName, callback?)
```

## apply()
- `operationName`: exact GraphQL operation name.
- `callback`: returns one of:
  - `{ requestParam, query }` to modify/forward to server, or
  - `{ response }` to short-circuit with mocked response.

## Response Structure
- Must follow GraphQL response shape with `data` and/or `errors`.
- Avoid mixing `response` with `requestParam/query`.

## Notes
- Multiple callbacks run in registration order.
- Use for validation, personalization, or test/mocking flows.
