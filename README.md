# YZ Blockchain (Braided Architecture)

A massively scalable, sharded distributed ledger system using Croquet consensus sessions and a "Braided" web of trust.

> **For a deep dive into the system design, see [architecture.md](./architecture.md).**

## Core Philosophy
- **No Global Finality**: Truth is local to a shard, but provably consistent across the network.
- **Homogeneous Execution**: All shards speak the same protocol and use the same native coin.
- **Braided Security**: Shards rely on a set of peer "Keeper Shards" (Notaries) to validate their history and evolution, rather than a central Beacon Chain.

## Architecture Overview

### 1. Shards (Croquet Sessions)
- **Role**: Each shard manages a subset of accounts and executes transactions.
- **Consensus**: Uses a "Sliding Window" mechanism.
    - **Validators**: 32 Active + 4 Alternates.
    - **Rotation**: 4 oldest validators retire each epoch; 4 alternates promote; 4 new alternates are recruited randomly from the DHT.

### 2. The Keeper System (Braided Trust)
- **Concept**: Every shard reports to $M$ (initially 3) **Keeper Shards**.
- **Function**: Keepers audit the **validator set transitions** of their subject shards.
- **Validity Receipts**: Keepers issue signed receipts certifying that a shard's evolution (epoch transition) was valid and followed the rules.
- **Cross-Shard Trust**: Shard B trusts Shard A because Shard A presents receipts from Shard C (a neutral Keeper).

### 3. Network Layer (DHT)
- **Backbone**: Kademlia-like Distributed Hash Table.
- **Roles**:
    - **Discovery**: Finding shards and Keepers.
    - **Pub/Sub**: Delivering cross-shard messages.
    - **Randomness**: The DHT address space provides entropy for selecting new validators.

## Transaction Flow (Alice -> Bob)

1.  **Execution**: Alice (Shard A) sends tokens. Shard A processes and finalizes the block.
2.  **Notarization**: Shard A gets **Validity Receipts** from its Keepers ($K_1, K_2, K_3$).
3.  **Transfer**: Shard A sends a proof bundle to Shard B.
    - Bundle includes: Transaction Proof + Validity Receipts.
4.  **Verification**: Shard B verifies the receipts (proving Shard A is a valid shard) and the transaction proof (proving Alice had funds).
5.  **Completion**: Shard B credits Bob.

## Getting Started

### Prerequisites
- Node.js v18+
- TypeScript

### Installation
```bash
npm install
```

### Roadmap
1.  **Nucleus**: Implement basic Shard (Croquet Session) and DHT Node.
2.  **Keeper Protocol**: Implement the Validity Receipt logic.
3.  **Cell Division**: Implement Shard Splitting and Keeper Inheritance.
