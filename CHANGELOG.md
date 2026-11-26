# Changelog

All notable changes to the published verifier are documented here.

## v1.1.0 — 2026-11-25

Explains server-attested receipts. No change to the receipt fingerprint, the
Merkle check, the manifest signature, or the signing key registry
(`KEYS.md`, `ipk-2026-01`, unchanged): every pack that verified under v1.0.0
verifies identically under v1.1.0.

- `verify_pack.py`
  (`sha256 07cbe5bd2a0a2807f3baecc6bd09d40ebdfa3fe510c44b927bb0aa4c8f984d98`):
  when a receipt carries no signature material and reports
  `server_attested: true`, the `[SKIP]` line now reads "server-attested event
  (<attestation_type>): no agent signature by design; covered by the signed
  manifest and Merkle proof" instead of "no signature material exposed". It
  stays a `[SKIP]`, never an `[OK]`: `server_attested` is display metadata,
  not bound by the receipt fingerprint, so the verifier does not treat it as
  proof. Receipts without the flag keep the previous message.
- `METHODOLOGY.md`
  (`sha256 3bac8adcd1bf523c55eef1e435c8b4ef244485907a5cc1e6bc4f041f1d3f6056`):
  new "Server-attested receipts" section (approval lifecycle and token
  consumption rows, what binds them, why they are a `[SKIP]`); receipt
  display fields such as `integrity_status`, `server_attested` and
  `attestation_type` documented as outside the fingerprint; pack layout lists
  `approval_chains/<audit_id>.json` and `export_report.json`, written by the
  new `build_evidence_pack.py export` step in the Inntris main repository.

## v1.0.0 — 2026-07-20

Initial publication.

- `verify_pack.py` — standalone offline evidence pack verifier,
  byte-identical to the copy embedded in every evidence pack
  (`sha256 332928c85e25149810a068cfe21f9d15aca31f083469bc9ee3d958f67165a366`).
- `METHODOLOGY.md` — verification methodology, byte-identical to the
  embedded copy
  (`sha256 4ca88e1630b922fef65abac38cf2a8708a7f04be1de473925f3b17eec4fe0801`).
- `KEYS.md` — canonical signing key registry, mirrored at
  `https://inntris.com/.well-known/inntris-keys.txt`.
- Repository CI verifies the published hashes, signing-key fingerprints,
  LF checkout rules, and dependency-free cryptographic fallbacks on Python
  3.10, 3.11, and 3.12.
- Relative to the previously embedded copy, `verify_pack.py` adds a startup
  known-answer self-test (RFC 8032 §7.1 TEST 3) to the pure-Python Ed25519
  fallback, mirroring the existing Keccak-256 self-test.

## Update procedure

The verifier and methodology are published in two places that must never
drift: embedded in every evidence pack (built from the Inntris main
repository) and in this repository. Any change follows this procedure, in
one sitting:

1. Change `evidence_pack/pack_contents/verify_pack.py` and/or
   `METHODOLOGY.md` in the Inntris main repository.
2. Update `verify_publication.lock` there with the new SHA-256 hashes —
   main-repository CI fails on any mismatch between the lock file and the
   embedded copies, so an unpublished change cannot land silently.
3. Copy the changed files byte-for-byte into this repository, update
   `SHA256SUMS`, and record the change in this file.
4. Update the hash lines mirrored at
   `https://inntris.com/.well-known/inntris-keys.txt`.
5. Tag a release here (`vX.Y.Z`) with the `SHA256SUMS` contents pasted into
   the release notes.

Key additions and retirements follow the rotation policy in `KEYS.md` and
also bump a release, so the registry's history is auditable through tags.
