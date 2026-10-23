# warrant-swap

Warrant for DeFi swaps: an agent proposes a token swap, and a fixed policy core decides what the signer can sign.

Status: **prototype, not on the critical path.** The first target case of Warrant is rent computation, not swaps.
The crates moved here from `policy-execution` without history. No swap has a guest, a proof or a contract.
The signer signs a warrant. Nothing on chain checks it.

## Crates

| Crate | Purpose |
|---|---|
| [`swap-verified`](swap-verified/README.md) | The swap rule evaluator and the timeout arithmetic (S11, S12), proved with Verus |
| [`swap-core`](swap-core/README.md) | Facts with provenance, safety checks S1 to S25 and S27, Bitcoin, EVM and Solana primitives, the policy DSL and warrant payloads |

Neither crate depends on `warrant-policy`, on RISC Zero or on a key. They read no network and no clock.
Time and chain state are inputs.

## Documents

- [`docs/defi-swaps.md`](docs/defi-swaps.md): the product flow for policy-controlled swaps.
- The specification and the implementation plan are in the [`warrant`](https://github.com/mnemonik-xyz/warrant/tree/main/swap) repository.

## Tests

```sh
cargo test --locked -p warrant-swap-verified -p warrant-swap-core
python3 swap-verified/verify.py --verus /path/to/verus --mutations   # needs Verus
python3 swap-core/check_mutations.py
```

Last run when the crates moved (2026-10-23): 144 tests pass in `swap-core`, 12 in `swap-verified`.
The Verus check did not run in that session because Verus was not installed. The earlier record is 52 obligations verified and 35 of 35 mutations rejected.

## Plan

`policy-execution/docs/framework.md` describes a common framework for all cases.
The swap case comes last in that plan. It needs a guest, a journal and an escrow, and the guest may be too large for the zkVM.
It will use a pinned `warrant-policy` release, as the other Warrant repositories do.
