---
title: "SDK error handling"
description: "Typed SDK errors — BadRequestError, AuthError, NoRouteError, RateLimitError, ServerError — and retry behavior."
llmDescription: "Error handling for the Argyros TypeScript SDK. All errors extend ArgyrosError. Documents typed subclasses by HTTP status: BadRequestError (400), AuthError (401/403), NoRouteError (404), RateLimitError (429), ServerError (5xx), with fields and when each is thrown. Covers automatic retry/backoff behavior and a catch-all pattern."
---

The SDK throws typed errors for different failure conditions. All errors extend `ArgyrosError`.

---

## Error Hierarchy

```
ArgyrosError (base)
├── BadRequestError    (400)
├── AuthError          (401/403)
├── NoRouteError       (404)
├── RateLimitError     (429)
└── ServerError        (5xx)
```

---

## Error Classes

### `ArgyrosError`

Base class for all SDK errors.

| Property | Type | Description |
|----------|------|-------------|
| `message` | `string` | Error message |
| `statusCode` | `number` | HTTP status code |
| `body` | `unknown` | Raw response body from the API |

### `BadRequestError` (400)

Thrown for invalid parameters: bad mint address, invalid amount, unsupported swap mode.

```typescript
import { BadRequestError } from "@argyros/sdk";

try {
  await sdk.quote({ inputMint: "invalid", ... });
} catch (err) {
  if (err instanceof BadRequestError) {
    console.log(err.message); // "invalid inputMint address"
  }
}
```

### `AuthError` (401/403)

Thrown when the API key is invalid, missing, or revoked.

```typescript
import { AuthError } from "@argyros/sdk";

try {
  await sdk.quote({ ... });
} catch (err) {
  if (err instanceof AuthError) {
    console.log(err.message); // "Invalid or missing API key"
  }
}
```

### `NoRouteError` (404)

Thrown when no liquidity path exists between the input and output tokens.

```typescript
import { NoRouteError } from "@argyros/sdk";

try {
  await sdk.quote({ ... });
} catch (err) {
  if (err instanceof NoRouteError) {
    console.log("No route found -- try a different token pair");
  }
}
```

### `RateLimitError` (429)

Thrown after all retry attempts are exhausted on rate-limited requests.

```typescript
import { RateLimitError } from "@argyros/sdk";

try {
  await sdk.quote({ ... });
} catch (err) {
  if (err instanceof RateLimitError) {
    console.log("Rate limited -- back off and retry later");
  }
}
```

### `ServerError` (5xx)

Thrown after all retry attempts are exhausted on server errors.

```typescript
import { ServerError } from "@argyros/sdk";

try {
  await sdk.quote({ ... });
} catch (err) {
  if (err instanceof ServerError) {
    console.log("Server error -- retry later");
  }
}
```

---

## Retry Behavior

The SDK automatically retries on transient failures before throwing:

| Status | Retried | Error thrown after exhaustion |
|--------|---------|------------------------------|
| 400 | no | `BadRequestError` (immediate) |
| 401/403 | no | `AuthError` (immediate) |
| 404 | no | `NoRouteError` (immediate) |
| 429 | yes | `RateLimitError` |
| 5xx | yes | `ServerError` |
| Network error / timeout | yes | `Error` |

Backoff is exponential: 1s, 2s, 4s, ... capped at 8s. With the default `retries: 2`, the SDK makes up to 3 attempts.

---

## Catching All Errors

```typescript
import {
  ArgyrosSDK,
  ArgyrosError,
  NoRouteError,
  RateLimitError,
  AuthError,
  BadRequestError,
  ServerError,
} from "@argyros/sdk";

try {
  const quote = await sdk.quote({ ... });
} catch (err) {
  if (err instanceof NoRouteError) {
    // No liquidity path
  } else if (err instanceof BadRequestError) {
    // Invalid parameters
  } else if (err instanceof AuthError) {
    // Bad API key
  } else if (err instanceof RateLimitError) {
    // Rate limited after retries
  } else if (err instanceof ServerError) {
    // Server error after retries
  } else if (err instanceof ArgyrosError) {
    // Other API error
    console.log(err.statusCode, err.message);
  } else {
    // Network error, timeout, etc.
  }
}
```
