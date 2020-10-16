# Introduction

<!--
  Copyright 2018-2020 Cargill Incorporated
  Licensed under Creative Commons Attribution 4.0 International License
  https://creativecommons.org/licenses/by/4.0/
-->

Splinter is the platform that will enable the next generation of distributed
applications.

With a purpose-built architecture anchored in values of privacy
and flexibility, Splinter provides a **privacy-first networking layer**, support
for **customized smart contracts** with configurable **consensus models**, and
**APIs for developing enterprise applications**.

Splinter's powerful feature set and agile architecture support a wide variety of
application patterns and integration with existing enterprise systems.
Learn about Splinter's key benefits:

* [True privacy with circuits](#true-privacy-with-circuits)
* [Distributed ledger platform as a service with Scabbard and
  WASM](#distributed-ledger-platform-as-a-service-with-scabbard-and-wasm)
* [Flexibility with Splinter's modular
   architecture](#flexibility-with-splinters-modular-architecture)
* [Smart contracts that can share state](#smart-contracts-that-can-share-state)
* [Easy application development with robust APIs and
  examples](#easy-application-development-with-apis-and-examples)
* [Event subscription for state changes](#event-subscription-for-state-changes)
* [Complex patterns for data flow](#complex-patterns-for-data-flow)
* [Auditable open source](#auditable-open-source)

You can also learn about [when Splinter might not be the right
choice](#why-not-splinter).

## True Privacy with Circuits

Splinter was designed with a privacy-first mindset, with a simple architecture
that reduces operational complexity.

**Circuits** are discrete connections between groups of Splinter nodes within a
larger network that provide private communication for the connected parties.
Only the members of a circuit have visibility into the circuit's transaction execution
and shared state. Not only are circuit messages and transactions private between
members &mdash; even circuit membership is hidden from non-circuit-member
Splinter nodes in the network.

Circuits make Splinter significantly different from traditional blockchain
platforms with privacy features that can leak information to management nodes or
administrators. Simply put, Splinter doesn't send private circuit information to
any non-circuit nodes.

Additional features of Splinter's circuit architecture:

* Splinter circuits are **fully decentralized**, for both operation and
  management. No separate management node is needed to order, preprocess,
  notarize, endorse, or validate transactions. Splinter circuits require only
  the participating nodes.

* Circuits are **dynamic** &mdash; they can be created as needed with a secure
  set of messages between participant nodes. Splinter nodes accept peer
  connections through a locked-down network management circuit. Nodes use this
  management circuit to propose a new circuit and agree to join a circuit.

* Circuit control can be **delegated to end users**, instead of being controlled
  by IT teams or centralized circuit managers. The nodes themselves use
  consensus to agree to the new circuit; no external administrator or
  "pre-peering" process is needed to create and manage circuits.

* Splinter circuits **share state privately** &mdash; with circuit members only.
  No central database is required for storing data changes or state information.

## Distributed Ledger Platform as a Service with Scabbard and WASM

Splinter uses **services** to provide distributed ledger functionality,
including transaction processing, a consensus mechanisms, and smart contracts.
This feature allows Splinter to flexibly support a variety of distributed ledger
models, unlike other platforms that embed their distributed ledger functionality
in the core architecture.

Out of the box, Splinter provides the Scabbard service as a distributed ledger
platform. Scabbard includes the following components:

* [Hyperledger Transact](https://github.com/hyperledger/transact), a
  platform-agnostic Rust library for executing transactions from on-ledger smart
  contracts with a smart contract engine. Transact handles all aspects of
  scheduling, transaction dispatch, and state management.

* [Sawtooth Sabre](https://github.com/hyperledger/sawtooth-sabre), a smart
  contract engine for WebAssembly (WASM) smart contracts

* [Two-phase commit
  protocol](https://en.wikipedia.org/wiki/Two-phase_commit_protocol),
  a simple agreement protocol (instead of a complex consensus algorithm) for
  stable, multi-party networks with trusted participants.

<!-- ------------------------------------------ -->

It's important to explain exactly what "distributed ledger" means for Scabbard,
because other platforms use this term differently.

With Scabbard, the distributed ledger implements a shared database using
public–private key cryptography, transaction rules, and an agreement protocol
(usually a consensus mechanism) for accepting those transactions.

Scabbard enables multi-party trust and provides a strong platform for
distributed applications. Scabbard is anchored in the following essential
principles:

* **State agreement is guaranteed with state proofs.**

  The distributed ledger has an underlying state model that uses a Merkle-Radix
  tree to store state data on each Splinter node. This model provides a _state
  root hash_ &mdash; a "roll-up" of current state &mdash; that is the cumulative
  hash of all data and child hash pointers in the tree. If any state data
  changes, all the hashes in that branch will change, which forms a new version
  of state.  As a result, the state root hash allows state proofs (also known as
  "Merkle proofs") to guarantee that all nodes agree on the current state.

* **Execution of business logic is governed by the rules for transactions and
  state agreement.**

  Business logic on a distributed ledger is represented by smart contracts that
  define the rules for actors and transactions. Scabbard stores each smart
  contract on the ledger as bytecode. Any authorized party can submit a smart
  contract to the ledger with a cryptographically signed transaction, which is
  handled the same way as any other transaction: validation, state proof, and
  agreement are required before committing the smart contract. Submitting a
  transaction for a smart contract executes it in a deterministic way, which
  guarantees that all nodes get the same result.

* **Agreement protocols enforce the consistency of transactions, state
  agreement, and smart contracts.**

  Scabbard uses an agreement protocol (a consensus algorithm or other method of
  reaching agreement) for nodes to reach agreement on all transactions,
  including those that submit a smart contract. The decision to commit is made
  on the basis of identical state proofs for all nodes.

But Scabbard's distributed ledger implementation is only the beginning.
Splinter's modular architecture can support a variety of distributed ledger
models, from Ethereum-style networks with virtual machines for running smart
contracts to platforms that use separate moderators or transaction-ordering
nodes. Because Splinter doesn't embed a specific distributed ledger in its core
architecture, adding a different style of distributed ledger is possible by
replacing the Scabbard service with a different service or set of services.

## Flexibility with Splinter's Modular Architecture

Splinter supports both single and multiple processes for distributed ledger
components. Other blockchain platforms have separate processes for the
validator, consensus engine, and smart contract engine (or transaction processor
functionality).

Scabbard provides transaction management functionality that runs as a single
process. Note that Splinter also supports multiple processes for distributed
ledger components.

Splinter's consensus API allows pluggable consensus, which means that a service
can provide a consensus engine for the algorithm or agreement protocol that best
suits the application. Most importantly, the consensus API is
"block agnostic" &mdash; it can accept data in any format, unlike platforms with
a built-in concept of blocks.

Splinter's modular architecture is designed to allow new data storage and
communication mechanisms.

* For data storage, Splinter's goal is to let application developers choose the
  appropriate storage mechanism.

* Because Splinter provides agnostic communication across various transport
  layers, such as TCP or TLS, it can easily be extended to additional transport
  layers.

## Smart Contracts That Can Share State

[Sawtooth Sabre](https://github.com/hyperledger/sawtooth-sabre), which is
included in the Scabbard service, implements **in-ledger smart contracts** that
are executed in a WebAssembly (WASM) virtual machine. Smart contracts can be
written in any language that can be compiled to WASM.

Scabbard's smart contracts are stored in state and executed by each participant
node in the circuit, rather than on a separate node or virtual machine. This
state is shared with all participants on the circuit, but the services for that
circuit ensure that the state is invisible to other Splinter nodes (those not
participating in the circuit).

Most importantly, a smart contract can **share state with other smart
contracts**. Each smart contract manages its own area of state (its own
_namespace_), but is not restricted to that state. A smart contract can access
another smart contract's state, as controlled by Sabre permissions. Splinter's
design for sharing state simplifies the complex approach of other blockchain
platforms, where sharing state means embedding one smart contract into another.

## Easy Application Development with APIs and Examples

Splinter includes several APIs for application development:

* **Service API** for building custom services to send and manage opaque
  messages between nodes on a circuit. The Scabbard service handles consensus,
  smart contracts, and transaction processing (with the two-phase commit
  agreement protocol, Sawtooth Sabre, and Hyperledger Transact, respectively).
  However, Splinter is designed to support other smart contract engines and
  transaction processing software.

* **Consensus API** for building and registering custom consensus protocols.
  The Scabbard service currently includes the multi-party [two-phase
  commit](https://en.wikipedia.org/wiki/Two-phase_commit_protocol) agreement
  protocol instead of a consensus protocol. The consensus API makes it easy to
  add other types of consensus, such as Byzantine Fault Tolerant (BFT) or Proof
  of Work (PoW).

* **Custom REST APIs** for extending services to external tools. For example,
  the Scabbard service uses this API to expose endpoints for smart contract
  submission.

Splinter also provides a growing list of optional features that are needed by
many application developers. Two examples:

* The **application authorization handler** manages notifications for pending
  circuit proposals and lets an application register for specific
  proposal-related events.

* The **Biome** component provides user and private key management, which helps
  bootstrap applications that need to add smart contract capabilities to an
  existing infrastructure that doesn't already include the concept of
  transactions signed by a user.

In addition, Splinter's experimental features allow optional access to new
functionality.  Because Splinter is undergoing rapid development, new
capabilities are initially added as experimental Rust features that are exposed
by the `splinter` library.  This lets developers access bleeding-edge features
in a controlled way, because these features often change before they are
stabilized. (Biome is an example of a Splinter component that was added as an
experimental feature.) Developer feedback is strongly encouraged for
experimental features.

Splinter provides [Gameroom]({% link docs/0.4/examples/gameroom/index.md %}) as
a complete example for building smart-contract-based applications with the
Splinter platform.

## Event Subscription for State Changes

As described earlier, the Scabbard service includes Hyperledger Transact for
transaction processing and state management. State agreement is achieved with
the Merkle-Radix tree, allowing multiple nodes or services to prove they have
the same data down to the last bit, cryptographically.

The Scabbard service implements a [Sawtooth-style event
system](https://sawtooth.hyperledger.org/docs/core/releases/1.0/architecture/events_and_transactions_receipts.html)
so that applications can subscribe to events of interest. **State delta export**
allows an application to materialize the state data from the Merkle-radix tree
to another database, event stream, log, or other mechanism. This allows
applications to query and analyze the smart contract state in their own
business-specific databases.

## Complex Patterns for Data Flow

Splinter has clear benefits when compared to traditional EDI systems and ETL
processes, which provide data- or outcome-focused data flows. Splinter enables
complex data flows based on transactions as well as data changes, which allows
partners to collaborate and update the data in a secure and verifiable way.

With Splinter, your application can import data, allow partners to collaborate
on changes (with verified state agreement), and export changes in the required
format. Smart contracts allow even more elaborate patterns for data generation
and updates.

## Auditable Open Source

Splinter is open source software, licensed under the [Apache License Version
2.0](https://github.com/Cargill/splinter/blob/master/LICENSE) software license.
The Splinter source code is freely available and auditable.

## Why NOT Splinter?

Even with all of its benefits, Splinter isn't perfect for all uses.
For example:

* If your consortium is comfortable with a centralized database owned by one
  organization or an intermediary, Splinter isn't the right choice, because
  you don't need a distributed blockchain application.

* If your data and transactions can be shared with a CSV file, the transactional
  nature of Splinter is more power than you need.

* If you like what you've learned about Splinter but need a mature platform,
  just wait a little while. Splinter will be ready for production use soon.
  Better yet, join us and help make Splinter work for you!
