# Railgun POI circuit artifacts — provenance

`03x03/` and `13x13/` are derived from Railgun's official POI circuit release:

- IPFS CID: `QmZ2MyM6TKxffkv6stuo2hFwmUfs3q4xgMYN164Sje8new`
  (`railgun-ppoi-circuit-artifacts` 0.0.1, with `transcripts/` 0001–0004 of the ceremony)
- Inputs: `circuits/<n>/zkey.br`, `circuits/<n>/vkey.json`, `prover/snarkjs/<n>.wasm.br`

## Reproduce

With the kohaku Railgun SDK (`crates/railgun`, binary `main` = `bin/convert_artifacts.rs`):

    cargo build --release -p railgun --bin main
    ./target/release/main https://ipfs-lb.com/ipfs/QmZ2MyM6TKxffkv6stuo2hFwmUfs3q4xgMYN164Sje8new <out-dir> 03x03 13x13

The converter parses each `.zkey` with `ark_circom::read_zkey`, writes the ark proving key and
constraint matrices (uncompressed serialization, brotli), copies the witness wasm as published,
and aborts unless the verifying key embedded in the proving key equals the release's
`vkey.json` field by field. `vkey.json` is kept next to each circuit for later checks:

    ./target/release/main --check-vk 03x03/proving_key.bin.br 03x03/vkey.json

## SHA-256

    dcb2f6e793fe47a8e32ba457085834dc11aebaab3336783fb9af592b7455b7c9  03x03/proving_key.bin.br
    0d306640d3e6783921facfb60bc01dfe20da5b86fc15258c07d85de0407da347  03x03/matrices.bin.br
    ff916dc7a95e593b70d0bdf6626bcba1104a22f33298fc1a2a64936325197493  03x03/wasm.br
    e6ac836dcabb1d70f3924f172d8edd52e09269f87a0d5b977650c63a8070bd31  03x03/vkey.json
    c676d692f4c6b15828ba55318f4ea905e2027a631b90befb7c5900911338bc9a  13x13/proving_key.bin.br
    591a1853808f7e67c787e4b7531ce330b49e16cb7b540da402dce5ed89751b35  13x13/matrices.bin.br
    96c8231557e0f990b0e6cb18fc45dae43f72c7d4c5638fd02753af7c80c2522a  13x13/wasm.br
    b939fbc34c9ba9cc294a8b5c1e72ddc4088e559d351dc4fab7b71234d34bbb17  13x13/vkey.json

## Note

These keys replace an earlier POI key set (same alpha/beta/gamma, different delta and circuit
constraints): proofs from one set do not verify against the other. Use them only with a POI node
that verifies against this release's `vkey.json`.

The loose files next to these folders (`03x03_proving_key.bin`, `03x03.wasm`, …) are from the
earlier set and are not read by the kohaku loader (which reads `<circuit>/*.br`).
