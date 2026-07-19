# Inntris Signing Keys

Pin verification against a key from this file. A pack whose manifest
signature does not verify against an ACTIVE key listed here must be
treated as unverified, regardless of its internal consistency.

## Active

| Key ID | Ed25519 public key (hex, 64 chars) | Effective from | Scope |
|--------|-----------------------------------|----------------|-------|
| ipk-2026-01 | `PENDING-PUBLICATION-DO-NOT-PIN` | 2026-07-XX | Evidence pack manifests |

## Retired

(none)

## Rotation policy

- New keys are added here with an effective date before first use.
- Retired keys move to the Retired table with a retirement date and
  remain valid for packs whose manifest timestamp predates retirement.
- This file is the single canonical registry. Copies elsewhere
  (inntris.com) mirror it; on any discrepancy, treat verification
  as failed and contact security@inntris.com.
