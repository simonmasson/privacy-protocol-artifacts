## Railgun circuit artifacts

Proving artifacts for the kohaku Railgun SDK (`RemoteArtifactLoader`), one folder per circuit:

    <circuit>/proving_key.bin.br   ark Groth16 proving key (uncompressed serialization, brotli)
    <circuit>/matrices.bin.br      R1CS constraint matrices (SerializableNpIndex, brotli)
    <circuit>/wasm.br              snarkjs witness generator, as published by Railgun

### Transaction circuits — `NNxMM/` (NN inputs × MM outputs, 01x01 … 05x05)
- Source: https://github.com/Railgun-Privacy/circuits-v2
- Release: https://ipfs-lb.com/ipfs/QmUsmnK4PFc7zDp2cmC4wBZxYLjNyRgWfs5GNcJJ2uLcpU/

### POI circuits — `poi/03x03/`, `poi/13x13/`
- Source: https://github.com/Railgun-Privacy/circuits-ppoi
- Release: https://ipfs-lb.com/ipfs/QmZ2MyM6TKxffkv6stuo2hFwmUfs3q4xgMYN164Sje8new/
  (`railgun-ppoi-circuit-artifacts` 0.0.1)
- Reproduction command and SHA-256 of every file: [`poi/PROVENANCE.md`](poi/PROVENANCE.md)

### Why pre-converted

`ark_circom::read_zkey` is slow (seconds per circuit, longer in wasm), so the `.zkey` of each
release is converted once into an ark proving key and matrices. The converter is
`crates/railgun/bin/convert_artifacts.rs` in the kohaku Railgun SDK:

    cargo build --release -p railgun --bin main
    ./target/release/main <ipfs-release-base> <out-dir> <circuit> [circuit…]

It aborts unless the converted key's verifying key equals the release's `vkey.json`, and prints
each output file's SHA-256. `main --check-vk <proving_key.bin.br> <vkey.json>` re-checks a key.
