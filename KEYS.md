# Inntris Signing Keys

Pin verification against a key from this file. A pack whose manifest
signature does not verify against an ACTIVE key listed here must be
treated as unverified, regardless of its internal consistency.

## Active

| Key ID | Ed25519 public key (hex, 64 chars) | SHA-256 fingerprint | Effective from | Scope |
|--------|-----------------------------------|--------------------|----------------|-------|
| ipk-2026-01 | `a2fce5d552515d456576f07ec754ac2c7da9e1c29f3d1018519d998f6f190732` | `089c7611802e5494a4274e6f1af45d514c2ffc8c208db8a50a0df2c4ec99151c` | 2026-07-20 | Evidence pack manifests |

## Retired

(none)

## Rotation policy

- New keys are added here with an effective date before first use.
- Retired keys move to the Retired table with a retirement date and
  remain valid for packs whose manifest timestamp predates retirement.
- This file is the single canonical registry. Copies elsewhere
  (inntris.com) mirror it; on any discrepancy, treat verification
  as failed and contact security@inntris.com.
