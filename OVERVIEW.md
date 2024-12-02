# Kakarot Scaffold Playground

## Overview

Kakarot is a zkEVM (Zero-Knowledge Ethereum Virtual Machine) written in Cairo. It is designed to enable seamless interoperability between the Cairo Virtual Machine (CairoVM) and the Ethereum Virtual Machine (EVM). 

This repository contains a collection of challenges that demonstrate how to write Solidity smart contracts capable of interacting with Cairo smart contracts leveraging the unique features of Kakarot. 

## Challenges

### 1. Voting System (easy)

**Objective:**

Write a Solidity smart contract that integrates seamlessly with a pre-written Voting smart contract in Cairo.

**Challenge Overview:**

- **Solidity Contract Scope:** Implement a contract in Solidity that interacts with the Cairo Voting contract. The Solidity contract should implement functions that call the relevant functions on the Cairo contract

- **Integration Focus:** Demonstrate how Solidity and Cairo smart contracts can interact effectively.

### 2. Token Faucets (easy)

**Objective:**

Write a Solidity smart contract that integrates seamlessly with a pre-written Token Faucet smart contract written in Cairo.

**Challenge Overview:**

- **Solidity Contract Scope:** Implement a contract in Solidity that interacts with the Cairo Token Faucet contract. The Solidity contract should implement functions that call the relevant functions on the Cairo contract

- **Integration Focus:** Demonstrate how Solidity and Cairo smart contracts can interact effectively.

### 3. Crownfunding Platform (meduim)

**Objective:**

Complete the implementation of a Crowdfunding platform written in Cairo and write Solidity smart contracts that integrates seamlessly with Crowdfunding platform.

**Challenge Overview:**

- **Cairo Contract Scope:** Implement campaign creation, fund locking, goal checking, withdrawal by campaign creators, and refund mechanisms for unmet goals in Cairo. Ensure secure handling of funds with clear conditions for withdrawals and refunds.

- **Solidity Contract Scope:** Write Solidity contracts to interact with the Cairo Crowdfunding platform, enabling functions for campaign creation, funds locking, goal checking, withdrawal by campaingn creators and refunding. Ensure user-friendly operations on the EVM side that map directly to Cairo functionalities.

- **Integration Focus:** Demonstrate seamless interoperability by enabling Solidity contracts to call and manage Cairo-based Crowdfunding operations, showcasing transparent fund management and cross-platform functionality.

### 4. Decentralized Marketplace (meduim)

**Objective:**

Complete the implementation of a Decentralized Marketplace written in Cairo and write Solidity smart contracts that integrate seamlessly with the Marketplace.

**Challenge Overview:**

- **Cairo Contract Scope:** Implement core features such as item listings, buy/sell functionality, and secure escrow. Include logic to prevent funds from being released to sellers until sale confirmation. Add an admin/marketplace owner module for dispute resolution.

- **Solidity Contract Scope:** Write Solidity contracts to interact with the Cairo Marketplace. Implement functions for item listings, buying and selling, and secure escrot, ensuring proper user interactions and secure fund transfers.

- **Integration Focus:** Demonstrate how Solidity and Cairo contracts can interact effectively to handle item transactions, enforce escrow security.

### 5. Decentralized Autonomous Organization (DAO) (meduim)

**Objective:**

Complete the implementation of a Decentralized Autonomous Organization (DAO) written in Cairo and write Solidity smart contracts that integrate seamlessly with the DAO.

**Challenge Overview:**

- **Cairo Contract Scope:** Implement core features including proposal creation, voting mechanisms, and proposal execution. Incorporate functionality for tracking members and voting power (based on tokens or NFT ownership), enforcing quorums, and configuring voting periods. Add governance security measures like time-locks, multisig approvals, and delays for proposal execution.

- **Solidity Contract Scope:** Implement the functionality required to facilitate interactions with the DAO platform, including managing proposals, enabling member participation in governance processes, and executing approved proposals. Incorporate mechanisms to track voting power and enforce governance rules such as quorums, voting periods, and security measures like time-locks and delayed execution.

- **Integration Focus:** Demonstrate seamless Solidity-Cairo interoperability for managing DAO governance. Showcase secure proposal management, voting processes, and execution through combined functionality across both platforms.