# Enclave Starter Template

This repository provides a starter template for building applications on the Enclave Protocol. It offers a starting point for developers to create, activate, and interact with E3 rounds within the protocol. The template includes both server-side infrastructure and tools for writing secure zkVM-based computations (using RISC Zero).

The **server** plays a critical role in:
1. Listening to protocol events (e.g., Key Publish, Input Publish).
2. Managing the progression of E3 rounds by taking actions like gathering encrypted votes, computing tallies, and publishing results.
3. Serving as a reliable nudge layer for the protocol.

Additionally, this repository includes the **RISC Zero-based voting application**, designed for securely processing and tallying encrypted votes off-chain.


## **Overview**

The repository structure is organized to support modular development, with separate packages for server-side functionality, zkVM guest programs, and smart contracts:

```plaintext
.
├── packages
│   ├── server          # Server implementation for interacting with the protocol
│   ├── risc0           # RISC Zero-based zkVM application and smart contracts
│   └── ...             # Additional dependencies and modules
```

Key directories:
- **`packages/server`**: Implements the server to listen to blockchain events, handle user inputs, and nudge the protocol forward.
- **`packages/risc0`**: Includes RISC Zero zkVM programs, smart contracts, and supporting libraries for secure computation.


## **Getting Started**

### **Dependencies**

Before you begin, ensure you have the following installed:

1. **Rust**: Install Rust via [rustup](https://rustup.rs/):
   ```sh
   curl https://sh.rustup.rs -sSf | sh
   ```

2. **Foundry**: Install Foundry via [Foundry's installation script](https://getfoundry.sh):
   ```sh
   curl -L https://foundry.paradigm.xyz | bash
   ```

3. **RISC Zero Tools**: Install the RISC Zero toolchain:
   ```sh
   curl -L https://risczero.com/install | bash
   rzup
   ```

Verify installation:
```sh
cargo risczero --version
```

### **Build the Server**
```bash
cd packages/server
cargo build
```

### **Build the zkVM Components**

Follow the [RISC Zero Foundry Template](https://github.com/risc0/risc0-foundry-template) for detailed documentation. A summary is provided below:

1. Update Git submodules:
   ```sh
   git submodule update --init
   ```

2. Build the zkVM program and Rust code:
   ```sh
   cd packages/risc0
   cargo build
   ```

3. Build the Solidity smart contracts (requires `cargo build` to be run first):
   ```sh
   forge build
   ```

## **Repository Structure**

### **Server (`packages/server`)**

The server acts as a "nudger," ensuring smooth protocol progression by:
- Listening to blockchain events (e.g., `E3 Key Published`, `Round Ended`).
- Aggregating encrypted user inputs.
- Processing tallies using zkVM logic and publishing results.

Key subdirectories:
- **`blockchain`**: Handles interactions with the blockchain, including event listeners and transaction relayers.
- **`routes`**: Defines API endpoints for interacting with the server.
- **`models`**: Contains data structures and models for the server.
- **`database`**: Manages the storage and retrieval of encrypted votes and results.

### **RISC Zero (`packages/risc0`)**

This package provides the zkVM environment for secure computations:
- **`apps`**: Interfaces for interacting with the zkVM and Ethereum.
- **`contracts`**: Smart contracts for verifying proofs and interacting with the Enclave Protocol.
    - **`CRISPRisc0.sol`**: Main contract for zkVM proof verification.
    - **`ImageID.sol`**: Contract for zkVM image identifiers.
- **`core`**: Core business logic for zkVM guest computations.
- **`methods`**: zkVM guest programs that run off-chain to perform secure computations.


## **Developing Your Application**

To build your application on the Enclave Protocol using this starter template, follow these steps:

1. **Set Up Interaction with the Enclave Contract**:
   - Implement a method for users to submit encrypted votes to the Enclave contract. This can be a web-based client, a mobile app, or another frontend solution.

2. **Run the Server**:
   - Use the server to handle protocol events and interact with the blockchain.
   - The server nudges the protocol forward by activating rounds and processing inputs.

3. **Write Your Secure zkVM Logic**:
   - Implement the core secure process logic in `packages/risc0/core/src/lib.rs`. This code runs off-chain in the RISC Zero zkVM and performs computations like vote tallying.
   - Write the zkVM guest program in `packages/risc0/methods/src/lib.rs`. This program is executed by the zkVM to perform secure computations.
   - Write the application logic in `packages/risc0/apps/src/lib.rs`. This code is executed by the server to interact with the zkVM.

4. **Deploy Smart Contracts**:
   - Write and deploy the on-chain logic in `packages/risc0/contracts/CRISPRisc0.sol`. This contract verifies zkVM proofs and publishes results back to the Enclave contract.

