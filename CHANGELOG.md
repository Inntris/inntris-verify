# Changelog

All notable changes to the published verifier are documented here.

## v1.0.0 — 2026-07-19

Initial publication.

- `verify_pack.py` — standalone offline evidence pack verifier,
  byte-identical to the copy embedded in every evidence pack
  (`sha256 332928c85e25149810a068cfe21f9d15aca31f083469bc9ee3d958f67165a366`).
- `METHODOLOGY.md` — verification methodology, byte-identical to the
  embedded copy
  (`sha256 4ca88e1630b922fef65abac38cf2a8708a7f04be1de473925f3b17eec4fe0801`).
- `KEYS.md` — canonical signing key registry, mirrored at
  `https://inntris.com/.well-known/inntris-keys.txt`.
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
