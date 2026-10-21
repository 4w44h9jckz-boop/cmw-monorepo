# Committee log (branch `committee/reshare-round`)

The working log of the session that builds the committees for the end-to-end runs in
[exp-e2e-frames](https://github.com/4w44h9jckz-boop/exp-e2e-frames). Commonware-native pieces
it needs live on this branch, under this repository's rules. This file is the branch's log, not
part of the library.

## 2026-10-21, first session

**Base.** This branch sits on upstream's release commit `v2026.9.0` (`d476a23`), not on this
fork's `main`: the crates kohaku-rs and the experiments build against are 2026.9.0 from
crates.io, and this fork's `main` has moved past that API (for one, `Player::finalize` lost its
batch-verifier parameter), so a `[patch.crates-io]` onto `main` does not build with them. The
branch was first pushed on `main` (`f3f7021`, `24426ba`) and then rebuilt on the release commit.

**Added: `bls12381::dkg::feldman_desmedt::run`** (ALPHA), with `Traffic`, `RunResult` and
`RunError`. It runs one honest DKG or Reshare round in memory and reports, for each participant,
the messages and encoded bytes it sent and received, and the three communication rounds. It is
for measuring a round and for tests, beside `deal`; a deployment still runs `Dealer` and
`Player` over a network. No new encoded type, so no conformance fixture.

Tests (`cargo test -p commonware-cryptography --lib run_test`, 4 passing):
- a DKG among 4, then a reshare dealt by a quorum of them to a disjoint 7, keeps the group
  key, and a quorum of the 7 signs under it;
- traffic: in a DKG of 4, every participant sends and receives 9 messages, and the bytes sent
  equal the bytes received; in a reshare from 3 dealers to 5 players, each dealer sends 10
  messages and receives 5;
- a reshare whose dealer has no share is refused;
- after a reshare, a quorum of the old committee that kept its shares still signs under the
  unchanged key: resharing erases nothing.

Checked: `cargo clippy -p commonware-cryptography --all-targets -- -D warnings`, `cargo fmt`,
and the crate built with `--cfg commonware_stability_BETA`, which compiles the new items out.
`just` is not installed here, so `just lint` was not run as such.

C3: partial | cmw-monorepo committee/reshare-round, cryptography/src/bls12381/dkg/feldman_desmedt.rs "run" | needs from wallet: none
