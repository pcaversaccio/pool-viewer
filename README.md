# Pool Viewer for Ethereum 2.0
Similar to a block explorer, but focused only on recent data. The exact definition of *recent* remains subject to discussion.

## Recent Data
Recent data should include:
> *Important caveat:* Staking ETH permits the staker to act as a validator on Ethereum's proof-of-stake (PoS) beacon chain, to support the Ethereum 2.0 upgrade, and to be eligible to earn staking rewards. The staked ETH (sETH2) **cannot** be un-staked and neither sETH2 nor any reward ETH2 may be transferred on the Ethereum network at this time. Thus, any transaction information, as we know it from e.g. Etherscan, will be subject to the implementation of Phase 1.5/2.0.
> - **Phase 1.5:** The legacy Ethereum chain becomes a shard on the new Ethereum blockchain. Expected end of 2021/early 2022. In this phase, the legacy Ethereum network will transition to a PoS network and be connected to the main Ethereum 2 chain.
> - **Phase 2:** The shards are fully functioning. Expected 2022+. The shard chains will be fully functioning and able to communicate with each other and run smart contracts.
> 
> The [`BeaconBlockBody`](https://github.com/ethereum/eth2.0-specs/blob/dev/specs/phase0/beacon-chain.md#beaconblockbody) currently consists of the following information that needs to be validated:
> ```python
> class BeaconBlockBody(Container):
>    randao_reveal: BLSSignature
>    eth1_data: Eth1Data  # Eth1 data vote
>    graffiti: Bytes32  # Arbitrary data
>    # Operations
>    proposer_slashings: List[ProposerSlashing, MAX_PROPOSER_SLASHINGS]
>    attester_slashings: List[AttesterSlashing, MAX_ATTESTER_SLASHINGS]
>    attestations: List[Attestation, MAX_ATTESTATIONS]
>    deposits: List[Deposit, MAX_DEPOSITS]
>    voluntary_exits: List[SignedVoluntaryExit, MAX_VOLUNTARY_EXITS]
>```
- **General:**
  - Total number of validators
  - Total number of pending validators
  - Total amount of ETH staked
  - Average account balance
  - Current slot
  - The most recent epoch
  - The most recent finalised epoch
- **At epoch level:**
  - Count of attestations per epoch number
  - Finality of epoch
  - Age of epoch
  - Percentage of validator participation in the given epoch
- **At slot level:**
  - TBD

## Ethereum 2.0 APIs
As summarised on the wishlist of the [Ecosystem Support Program](https://esp.ethereum.foundation/en/wishlist/), there are multiple ways to connect with the Ethereum 2.0 blockchain:
- **Ethereum 2.0 Networking REPL:** REPL is a *Read-Eval-Print Loop* (REPL), a programming environment that enables you to interact with Ethereum 2.0 components by evaluating one code expression at a time. The go-to-source for this approach is [Rumor](https://github.com/protolambda/rumor), an interactive shell written in Golang.
- **Gossipsub Protocol:** Gossipsub is an extensible baseline pubsub (publish–subscribe) protocol, based on randomised topic meshes and gossip. There are multiple implementations available ([source](https://github.com/libp2p/specs/tree/master/pubsub/gossipsub)):

    Legend: ✅ = complete, 🏗 = in progress, ❕ = not started yet

    | Name                                                                                             | v1.0  | v1.1  |
    |--------------------------------------------------------------------------------------------------|:-----:|:-----:|
    | [go-libp2p-pubsub (Golang)](https://github.com/libp2p/go-libp2p-pubsub/blob/master/gossipsub.go) |   ✅  |   ✅  |
    | [js-libp2p-gossipsub (JavaScript)](https://github.com/ChainSafe/js-libp2p-gossipsub)                    |   ✅  |   ✅  |
    | [rust-libp2p (Rust)](https://github.com/libp2p/rust-libp2p/tree/master/protocols/gossipsub)      |   ✅ |   🏗  |
    | [py-libp2p (Python)](https://github.com/libp2p/py-libp2p/tree/master/libp2p/pubsub)              |   ✅  |   🏗  |
    | [jvm-libp2p (Java/Kotlin)](https://github.com/libp2p/jvm-libp2p/tree/develop/src/main/kotlin/io/libp2p/pubsub) |   ✅  |   🏗  |
    | [nim-libp2p (Nim)](https://github.com/status-im/nim-libp2p/blob/master/libp2p/protocols/pubsub/gossipsub.nim) |   ✅  |   🏗  |

- **Ethereum 2.0 Beacon Node API:** The beacon node API allows for interactions between beacon nodes, including p2p networking connectivity as well as getting current beacon chain state and blocks from other nodes. There are multiple node implementations that can be used (see also [here](https://ethereum.org/en/eth2/get-involved/#clients)):
  > *Difference between beacon nodes and validator clients:* Beacon nodes are connected via p2p to other beacon nodes while validator clients maintain a dedicated connection with a single beacon node. The validator client is more or less equivalent to the miner on the Ethereum 1.0 blockchain.
  - [Nimbus](https://github.com/status-im/nimbus-eth2) - Nim Ethereum 2.0 node implementation
  - [Teku](https://github.com/ConsenSys/teku) - Java Ethereum 2.0 node implementation
  - [Lighthouse](https://github.com/sigp/lighthouse/) - Rust Ethereum 2.0 node implementation
  - [Prysm](https://github.com/prysmaticlabs/prysm/) - Golang Ethereum 2.0 node implementation
  - [Cortex](https://github.com/NethermindEth/cortex) - C# (.NET) Ethereum 2.0 node implementation
  - [Lodestar](https://github.com/ChainSafe/lodestar) - TypeScript Ethereum 2.0 node implementation
  - [Trinity](https://github.com/ethereum/trinity) - Python Ethereum 2.0 node implementation
  > There is an effort to standardise an Ethereum 2.0 API for all node implementations [here](https://github.com/ethereum/eth2.0-apis), and all the above nodes are currently working on implementing it into their codebase. However, for the meantime, they use client-specific APIs. E.g. in the [Prysm](https://docs.prylabs.network/docs/how-prysm-works/ethereum-2-public-api) beacon node, [`ethereumapis`](https://github.com/prysmaticlabs/ethereumapis) is the only supported API schema currently.
- **Third-Party API Providers:** Another option would be to retrieve all the necessary data via third-party API providers such as [Infura](https://infura.io/) or [Alechemy](https://www.alchemy.com/). Infura already provides an API service via a [Teku node implementation](https://infura.io/docs/eth2).

### Proposed Path
The focus of this project is the user-friendly visualisation of recent Ethereum 2.0 transactions and not the technical backbone of Ethereum 2.0 itself. Therefore, we will avoid using a networking REPL or Gossipsub protocol directly. To ensure a sustainable implementation with minimal technical debt and at the same time preserve the open-source notion, it is preferable that we wait until a standardised Ethereum 2.0 API is built into the node implementations. This will allow developers to clone the repository and link their own node implementation without any complex reconfigurations in the future. From a risk management point of view, in the long run we should have a combination of different node implementations (2-3 different implementations) & third-party APIs to avoid wrong visualisations (which could be caused by a faulty implementation). However, if standardisation of the API takes too long, I would suggest starting with the Prysm implementation.

## License
The `pool-viewer` implementation is licensed under the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also included in our repository in the [`LICENSE`](https://github.com/pcaversaccio/pool-viewer/blob/main/LICENSE) file.
