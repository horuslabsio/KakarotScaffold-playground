# Challenge 1: Token Faucets

In this challenge, we will create a Token Faucet, a simple smart contract that allows users to claim a small amount of tokens periodically. Token faucets are often used in testnets to provide developers with tokens to test their applications.

The faucet contract will use the governance token created in `Challenge 0:Decentralized Governance` and allow eligible users to request tokens, with safeguards to prevent abuse.

> [!NOTE]
> The aim of this challenge is to demonstrate cross-interaction between Cairo and Solidity using Kakarot, but cross-interaction between Cairo and Solidity is still a work in progress. This challenge only includes the Solidity example for now. Once Kakarot fully supports interoperability, the challenge can be extended to enable seamless cross-VM functionality between Cairo and Solidity.

### Objectives

- Create a token faucet contract that allows users to claim small of tokens periodically.
- Deploy and interact with the token faucet contract on Kakarot.

### Prerequisites

Ensure you have the following:

- [Node.js](https://nodejs.org/en)
- [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
- Completed the Decentralized Governance Challenge
- Basic knowledge of Solidity and Ethereum Virtual Machine (EVM)

## Checkpoint 0: Setup Environment

1.  Install Starknet Scaffold

    Scaffold your project with the following command:
    ```bash
    npm i create-starknet-app
    ```

2.  Install Kakarot Package

    Use the `create-starknet-app` command to initialize your Kakarot project:
    ```bash
    npx create-starknet-app
    ```

    - Enter your package name when prompted

    - Select the `Kakarot` package by entering `5` in the prompt.

    - Start Kakarot Development Environment

      Navigate to your project directory and start the environment:

      ```bash
      cd your-package-name
      npm run start-kakarot
      ```

## Checkpoint 1: Token Faucet Contract

1.  Navigate to the Solidity Contracts Directory

    ```bash
    cd kakarot/solidity_contracts/src/examples
    ```

2.  Create the Token Faucet Contract

    Create a new Solidity file:

    ```bash
    touch TokenFaucet.sol
    ```

    Add the following code to `TokenFaucet.sol`:

    ```JavaScript
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "./GovernanceToken.sol";
    import "@openzeppelin/contracts/access/Ownable.sol";

    contract TokenFaucet is Ownable {
        GovernanceToken public governanceToken;
        uint256 public maxClaimAmount;
        uint256 public cooldownTime;

        mapping(address => uint256) public lastClaimTime;

        event TokensClaimed(address indexed user, uint256 amount);

        constructor(address _governanceToken, uint256 _maxClaimAmount, uint256 _cooldownTime) {
            governanceToken = GovernanceToken(_governanceToken);
            maxClaimAmount = _maxClaimAmount;
            cooldownTime = _cooldownTime;
        }

        modifier canClaim() {
            require(block.timestamp >= lastClaimTime[msg.sender] + cooldownTime, "Claim cooldown active");
            _;
        }

        function claimTokens() external canClaim {
            require(governanceToken.balanceOf(address(this)) >= maxClaimAmount, "Faucet out of tokens");
            
            lastClaimTime[msg.sender] = block.timestamp;
            governanceToken.transfer(msg.sender, maxClaimAmount);

            emit TokensClaimed(msg.sender, maxClaimAmount);
        }

        function refillFaucet(uint256 amount) external onlyOwner {
            require(governanceToken.balanceOf(msg.sender) >= amount, "Insufficient tokens");
            governanceToken.transferFrom(msg.sender, address(this), amount);
        }

        function setMaxClaimAmount(uint256 _maxClaimAmount) external onlyOwner {
            maxClaimAmount = _maxClaimAmount;
        }

        function setCooldownTime(uint256 _cooldownTime) external onlyOwner {
            cooldownTime = _cooldownTime;
        }
    }
    ```

## Checkpoint 2: Deploy the Faucet Contract

1.  Deploy Token Faucet contract

    To deploy the token faucet contract, navigate to your package's base directory and run

    ```bash
    npm run deploy-kakarot-evm-contract --contract-path=kakarot/solidity_contracts/src/examples/TokenFaucet.sol:TokenFaucet --constructor-args=<GOVERNANCE_TOKEN_ADDRESS> 1000000000000000000 86400 --rpc-url=<YOUR_RPC_URL> --private-key=<YOUR_PRIVATE_KEY>
    ```

    Replace
    
    - <GOVERNANCE_TOKEN_ADDRESS> with the address of the governance token contract from `Decentralized Governance` challenge
    - <YOUR_RPC_URL> with `http://127.0.0.1:3030` to deploy to the local RPC or `https://sepolia-rpc.kakarot.org` to deploy to Kakarot Starknet Sepolia
    - <YOUR_PRIVATE_KEY> your private key
