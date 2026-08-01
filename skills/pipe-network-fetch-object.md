---
name: pipe-network-fetch-object
description: Retrieve an object from Pipe Network storage by name, hash, operation id, or URL.
api: Pipe Storage (Firestarter)
method: generated
source: https://github.com/PipeNetwork/lib-storage
operations:
- auth_verify
- fetch
- delete
---

# Fetch (and optionally delete) a Pipe object

Downloads a stored object using any supported key. Operation names are real SDK
methods.

## Steps

1. Ensure an authenticated session (see pipe-network-store-and-share: SIWS
   `auth_challenge` -> `auth_verify`, or `PIPE_API_KEY` + `PIPE_ACCOUNT`).
2. `fetch(key)` where `key` may be a file name, Blake3 content hash, operation
   id, or a deterministic URL -> returns the object bytes.
3. Optional cleanup: `delete(key)` removes an object by file name or operation
   id.

## Rules

- Downloads stream directly to disk in the CLI (`download-file`); use `--legacy`
  only if the streaming endpoint fails.
- Encrypted objects must be decrypted client-side (`--decrypt` in pipe-cli) with
  the same password/key used at upload (AES-256-GCM or PQC Kyber-1024).
- A 404 means no object matches the key; confirm with `list-uploads` /
  `file-info`.
