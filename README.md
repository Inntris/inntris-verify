# inntris-verify

This repository is the canonical, out-of-band publication channel for the
Inntris evidence pack verifier and its signing keys.

`verify_pack.py` verifies an Inntris evidence pack with zero Inntris
dependencies — no Inntris endpoint, no Inntris library, no network at all,
except one optional on-chain check that talks only to a Base RPC node of the
verifier's choosing. Verification that routes through the vendor of the
evidence is circular; this script exists so it never has to. The copy in this
repository is byte-identical to the copy embedded in every evidence pack, so
you can audit it here once and diff it against any pack you receive.

## Quickstart (offline, pinned)

```
python3 verify_pack.py path/to/pack.zip --pubkey <hex from KEYS.md>
```

Requires stock Python 3.10 or newer, standard library only — nothing to
install. Exit code 0 means every attempted check passed. This checks, in
order: the Ed25519 signature over `manifest.json` against the pinned key;
every file's SHA-256 against the signed manifest (both directions — nothing
missing, nothing smuggled in); every receipt's canonical fingerprint; every
agent signature; and every Merkle inclusion proof rebuilt leaf-to-root with
keccak256.

## On-chain anchor check (optional)

```
python3 verify_pack.py path/to/pack.zip --pubkey <hex> --rpc <your Base RPC URL>
```

Adds a raw JSON-RPC `eth_call` to `AnchorRegistry.getBatch(bytes32)` for each
anchored Merkle root in the pack, against a Base (chain id 8453) RPC node you
choose. The contract is `AnchorRegistry` at
`0x0600eA15802c8d2EA429371b2EB0aacCFe321480` on Base mainnet. The check fails
unless the contract reports a nonzero batch for the root.

## Why pinning matters

Without `--pubkey`, the pack self-verifies against the key embedded in the
pack, and the verifier prints exactly this:

```
[WARN] no --pubkey pinned: the signature proves internal consistency only. Cross-check this key fingerprint against Inntris's published keys: <fingerprint>
```

A forger who rebuilt an entire pack could embed their own key, and the pack
would still be internally consistent. Pinning `--pubkey` to a key published
in this repository closes that loop: the signature then proves the manifest
was signed by the key Inntris published out-of-band, not merely by whatever
key the pack carries.

## Trust surface for auditors

The verifier is a single file, ~650 lines, Python standard library only. That
total includes embedded pure-Python fallback implementations of Keccak-256
and Ed25519 (RFC 8032, verify-only); both fallbacks self-test at startup
against known answers (`keccak256(b"")` and RFC 8032 §7.1 TEST 3) and abort
if the self-test fails. If you install `pynacl` and `eth-hash`, the fallbacks
are not used and the surface you need to audit shrinks to roughly 400 lines
of verification logic plus two widely-audited libraries.

## Integrity of this repository

`SHA256SUMS` pins the two files that must stay byte-identical to the copies
embedded in every evidence pack:

```
332928c85e25149810a068cfe21f9d15aca31f083469bc9ee3d958f67165a366  verify_pack.py
4ca88e1630b922fef65abac38cf2a8708a7f04be1de473925f3b17eec4fe0801  METHODOLOGY.md
```

The same hashes are mirrored, alongside the signing keys, at
<https://inntris.com/.well-known/inntris-keys.txt>. Check them with
`sha256sum --check SHA256SUMS` after cloning. The Inntris main-repository CI
fails if the embedded copies drift from these hashes, so any change to the
verifier forces a conscious re-publication here.

## Cross-check the key registry

Before pinning, compare `KEYS.md` in this repository against the mirror at
<https://inntris.com/.well-known/inntris-keys.txt>. The two channels are
independently controlled; an attacker must compromise both to substitute a
key. On any discrepancy between them, treat verification as failed and
contact security@inntris.com.
