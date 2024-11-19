# Challenge 0: Decentralized Governance

Decentralized governance is a cornerstone of cryptocurrency projects, enabling token holders to actively participate in decision-making processes. This challenge guides you through building a simple voting system using Kakarot, leveraging its dual-VM capability to deploy a contract on Cairo while interacting with it from an EVM-compatible environment.

> [!NOTE]
> Cross-interaction between Cairo and Solidity is still a work in progress. This challenge only includes the Solidity example for now. Once Kakarot fully supports interoperability, the governance contract can be extended to enable seamless cross-VM functionality.

### Objectives

- Create a decentralized governance system using ERC20 tokens for voting power.
- Deploy and interact with governance contracts in a dual-VM environment (Cairo and EVM).

### Prerequisites

Ensure you have the following:

- [Node.js](https://nodejs.org/en)
- [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
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

## Checkpoint 1: Create Governance Token

Governance tokens are used as voting power in the system. You will create an ERC20-compliant token with minting capabilities.

1.  Navigate to the Solidity Contracts Directory

    ```bash
    cd kakarot/solidity_contracts/src/examples
    ```

2.  Create the Governance Token Contract

    Create a new Solidity file:

    ```bash
    touch GovernanceToken.sol
    ```

    Add the following code to `GovernanceToken.sol`:

    ```JavaScript
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
    import "@openzeppelin/contracts/access/Ownable.sol";

    contract GovernanceToken is ERC20, Ownable {
        constructor(string memory name, string memory symbol, uint256 initialSupply) ERC20(name, symbol) {
            _mint(msg.sender, initialSupply);
        }

        // Mint additional tokens (if needed, for new participants)
        function mint(address to, uint256 amount) external onlyOwner {
            _mint(to, amount);
        }
    }
    ```

## Checkpoint 2: Implement Governance Contract

The governance contract will manage proposals and voting.

1.  Create the Governance Contract

    Create a new Solidity file:

    ```bash
    touch Governance.sol
    ```

    Add the following code to `Governance.sol`:

    ```JavaScript
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    import "./GovernanceToken.sol";

    contract Governance {
        struct Proposal {
            uint256 id;
            string description;
            uint256 voteCountYes;
            uint256 voteCountNo;
            uint256 startTime;
            uint256 endTime;
        }

        mapping(uint256 => Proposal) public proposals;
        mapping(address => mapping(uint256 => bool)) public hasVoted;
        uint256 public proposalCount;
        GovernanceToken public governanceToken;
        uint256 public votingDuration = 7 days;

        event ProposalCreated(uint256 indexed id, string description, uint256 startTime, uint256 endTime);
        event Voted(uint256 indexed proposalId, address indexed voter, bool vote, uint256 weight);

        constructor(address _governanceToken) {
            governanceToken = GovernanceToken(_governanceToken);
        }

        modifier onlyTokenHolders() {
            require(governanceToken.balanceOf(msg.sender) > 0, "Only token holders can call this function");
            _;
        }

        // Create a new proposal
        function createProposal(string memory description) external onlyTokenHolders {
            proposalCount++;
            uint256 startTime = block.timestamp;
            uint256 endTime = startTime + votingDuration;

            proposals[proposalCount] = Proposal({
                id: proposalCount,
                description: description,
                voteCountYes: 0,
                voteCountNo: 0,
                startTime: startTime,
                endTime: endTime
            });

            emit ProposalCreated(proposalCount, description, startTime, endTime);
        }

        // Vote on a proposal
        function vote(uint256 proposalId, bool support) external onlyTokenHolders {
            Proposal storage proposal = proposals[proposalId];

            require(block.timestamp >= proposal.startTime, "Voting has not started");
            require(block.timestamp <= proposal.endTime, "Voting has ended");
            require(!hasVoted[msg.sender][proposalId], "Already voted");

            uint256 voterWeight = governanceToken.balanceOf(msg.sender);
            require(voterWeight > 0, "No voting power");

            if (support) {
                proposal.voteCountYes += voterWeight;
            } else {
                proposal.voteCountNo += voterWeight;
            }

            hasVoted[msg.sender][proposalId] = true;

            emit Voted(proposalId, msg.sender, support, voterWeight);
        }

        // Get details of a proposal
        function getProposal(uint256 proposalId)
            external
            view
            returns (
                string memory description,
                uint256 voteCountYes,
                uint256 voteCountNo,
                uint256 startTime,
                uint256 endTime
            )
        {
            Proposal memory proposal = proposals[proposalId];
            return (proposal.description, proposal.voteCountYes, proposal.voteCountNo, proposal.startTime, proposal.endTime);
        }
    }
    ```

## Checkpoint 3: Deploy Contracts

1.  Deploy Governance Token contract

    To deploy the governance token contract, navigate to your package's base directory and run

    ```bash
    npm run deploy-kakarot-evm-contract --contract-path=kakarot/solidity_contracts/src/examples/GovernanceToken.sol:GovernanceToken --constructor-args="\"Governance Token\" GOV 1000000000000000000000000" --rpc-url=<YOUR_RPC_URL> --private-key=<YOUR_PRIVATE_KEY>
    ```

    Replace <YOUR_RPC_URL> and <YOUR_PRIVATE_KEY> accordingly:

2.  Deploy Governance contract

    To deploy the governance contract, navigate to your package's base directory and run

    ```bash
    npm run deploy-kakarot-evm-contract --contract-path=kakarot/solidity_contracts/src/examples/Governance.sol:Governance --constructor-args=<GOVERNANCE_TOKEN_CONTRACT_ADDRESS> --rpc-url=http://127.0.0.1:3030 --private-key=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
    ```
