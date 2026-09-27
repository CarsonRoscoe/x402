---
name: reviewing-mcp
description: Review an x402 MCP client, server, or transport change. Use when a PR touches MCP packages or wires payment challenges through MCP tool calls in TypeScript, Go, or Python.
---

# Reviewing MCP

MCP is another transport for the same payment flow as HTTP. Carson and Philippe both block merges that invent a third semantics. Cross-language hook names are part of the API.

## What they check

- Verify failure stops the call. Do not settle after an unsuccessful verify (#1056).
- The unpaid challenge matches HTTP: a 402-style error and no content body, not a successful tool result (#1056).
- Type guards are sound. If `isPaymentRequiredError` accepts both `402` and MCP `-32042`, the type is a union. Do not hide the mismatch behind `as unknown` (#1728).
- Non-payment errors propagate. `getToolPaymentRequirements` must rethrow when the error is not a payment challenge. Returning null makes a broken tool look free. A test that locks in the swallow is a bug, not coverage (#1728).
- Hook names match TypeScript and Go: `on_payment_required`, `on_before_payment`, `on_after_payment` (#1131). Internal slice names may differ; the public hooks may not.
- `auto_payment=False` with no payment hook warns at init, and the error text tells the caller to enable auto payment or register a hook (#1131).
- Lifecycle methods (`connect` / `close`) may be no-ops on a transport that does not need them. Methods that return data raise if unsupported (#1131).
- Extension validation runs on MCP, not only on HTTP. Go must call `ValidateExtensions` and return a 402 with `invalidReason`, as TypeScript does (#3115).
- `OnAfterExecution` runs in the same place relative to an error result as TypeScript. Skipping it when the handler sets `IsError` is a behavior split (#3115).
- Payment headers on retry match the other client interceptors (#1131, compared with the TypeScript axios client).

## What they block on

- Settling after a failed verify, or returning content with the challenge (#1056).
- An unsound type guard or a catch that turns every exception into "not a payment" (#1728).
- MCP silently skipping extension checks the HTTP server performs (#3115).
- Checked-in e2e client binaries. Build them in `setup.sh` if a binary is useful (#1149).

## What they accept

- An example that demonstrates one use of the client, as long as the docstring says it is an example and not the only legal shape (#1056).
- Stricter validation than a type guard, when offered (#1056).
- Leaving an internal naming inconsistency alone when every SDK shares it, and cleaning all three languages in one later PR (#1131).
- A warning plus an actionable error instead of a new required constructor argument (#1131).

## Signals

- #1056 initial MCP client/server: success check before settle, 402 with no content.
- #1728 `McpError(-32042)`: widen the type, rethrow non-payment errors.
- #1131 Python MCP: hook parity, init warning, no-op lifecycle versus raising data methods.
- #3115 Go payment flows: `ValidateExtensions` on MCP, `OnAfterExecution` order, dynamic extra fields aligned with TypeScript.
- #1149 MCP e2e: no committed binaries; delete shims left from an unofficial SDK.
