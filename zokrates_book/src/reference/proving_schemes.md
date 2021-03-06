# Proving schemes

## Curves

Proving schemes supported by ZoKrates require a pairing-friendly elliptic curve. The options are the following:

### ALT_BN128
This curve is supported by Ethereum.

### BLS12_381
This curve is *not* supported by Ethereum and is currently only available for the G16 [proving scheme](#schemes).

## Schemes

ZoKrates supports different proving schemes. All of the available schemes rely on the ALT_BN128 curve, which means that they're all compatible with Ethereum.

We identify the schemes by the reference to the paper that introduced them. Currently the options available are:

| Name | CLI flag | Requires libsnark | Curves |
| ---- | -------- | ----------------- | ------ |
| [PGHR13](https://eprint.iacr.org/2013/279) | `--proving-scheme pghr13` | Yes | ALTBN_128 |
| [G16](https://eprint.iacr.org/2016/260) | `--proving-scheme g16` | No | ALTBN_128, BLS12_381 |
| [GM17](https://eprint.iacr.org/2017/540) | `--proving-scheme gm17` | Yes | ALTBN_128 |

When not using the default, the CLI flag has to be provided for the following commands:
- `setup`
- `export-verifier`
- `generate-proof`

## Supporting backends

As shown in the table above, the `PGHR13` and `GM17`schemes require [libsnark](https://github.com/scipr-lab/libsnark) as a backend, while G16 uses [bellman](https://github.com/zkcrypto/bellman), which is included as the default backend.

To include libsnark in the build, compile ZoKrates from [source](https://github.com/ZoKrates/ZoKrates/) with the `libsnark` feature:
```bash
cargo +nightly -Z package-features build --release --package zokrates_cli --features="libsnark"
```
 Note, that this is only tested for Linux. If you are on another OS, consider using our Docker container, which includes a libsnark installation.

## G16 malleability

When using G16, developers should pay attention to the fact that an attacker, seeing a valid proof, can very easily generate a different but still valid proof. Therefore, depending on the use case, making sure on chain that the same proof cannot be submitted twice may *not* be enough to guarantee that attackers cannot replay proofs. Mechanisms to solve this issue include:
- signed proofs
- nullifiers
- usage of an ethereum address as a public input to the program
- usage of non-malleable schemes such as GM17
