---
name: pipe-network-store-and-share
description: Upload a file to Pipe Network decentralized storage and return a public deterministic URL.
api: Pipe Storage (Firestarter)
method: generated
source: https://github.com/PipeNetwork/lib-storage
operations:
- auth_challenge
- auth_verify
- store
- pin
---

# Store a file on Pipe and share it

Uploads bytes to Pipe's content-addressed storage and resolves a public,
deterministic URL for the object. All operation names below are real SDK
methods from `@pipe-network/agent-storage` / `pipe-agent-storage`.

## Steps

1. Authenticate (SIWS, preferred):
   - `auth_challenge(wallet_public_key)` -> get `nonce` + `message`.
   - Sign `message` with the Solana wallet.
   - `auth_verify(wallet_public_key, nonce, message, signature_b64)` -> JWT
     access + refresh tokens (used automatically for later calls).
   - Compatibility mode: skip SIWS and set `PIPE_API_KEY` (user_app_key) +
     `PIPE_ACCOUNT`.
2. `store(data)` -> uploads, polls to completion, returns the Blake3 content
   hash + deterministic URL.
3. `pin(key)` -> resolve any key (file name / hash / operation id) to the public
   URL `https://{node}/{account}/{hash}`.

## Rules

- On HTTP 401 the SDK auto-refreshes the JWT once and retries; a persistent 401
  means re-run `auth_challenge` -> `auth_verify`.
- Uploads are billed against prepaid USDC credits; check with `credits-status`
  and top up before large uploads. A 402 means insufficient credits.
- Identical bytes yield the same Blake3 hash + URL (content addressing), so
  re-storing is naturally de-duplicated.
