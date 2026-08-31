# dln-node-knots-e2e

End-to-end scenarios for [`dln-node-knots`](https://github.com/DarkWebDivingClub/dln-node-knots)
against **BTK** — Bitcoin Knots after its BLAKE2b consensus split.

## Scenarios

- `knots_backend` — the node against a Knots chain at level 0, where
  `Blake2bHeight` is unreached and every header is the historical 80-byte
  form. At level 0 a Knots chain is behaviourally Bitcoin Core, and the
  scenario asserts that rather than assuming it.
- `activation` — BLAKE2b activating underneath a running node: headers
  switch to 164 bytes mid-scenario, both nodes keep following the chain, a
  channel opened beforehand stays active with its funding transaction in
  the same block, a payment settles on the far side, a node started cold
  against an activated chain syncs to it, and a channel opened entirely
  under v2 headers works.

```
KNOTS_FEATURES=blake2b cargo run --bin activation
```

Each prints `PASS` or `FAIL` and sets its exit status.

## The feature flag matters

`activation` requires the node built with `blake2b`, which turns on
`bitcoin/blake2b` throughout its dependency tree. **Built without it the
scenario fails**, and that is deliberate: a node that cannot parse a v2
header stops following the chain the moment the format changes, so a
passing run with the feature off would mean the test was not testing the
chain.

`knots_backend` runs either way, and should — level 0 is v1 headers
whatever the node was built with.

## Scope

**The node against a BTK chain.** Not the node in general, and not the
exchange:

| Repo | Covers |
|---|---|
| [`dln-node-e2e`](https://github.com/DarkWebDivingClub/dln-node-e2e) | the node itself, on a Core chain |
| [`diamond-x-e2e`](https://github.com/DarkWebDivingClub/diamond-x-e2e) | the exchange, and the shared harness |

The cross-chain swap with a real BTK leg lives with the exchange, since
it needs both chains.

## Requirements

Docker, and a Knots regtest image built from a local Knots checkout —
`docker/knots/build.sh` in `diamond-x-e2e`. The specification of the
header change is [BKIP-0001](https://github.com/DarkWebDivingClub/bkips/blob/master/bkip-0001.md).

## Licence

GPL-3.0-only.
