---
id: "blockchain"
title: "Blockchain"
slug: "/fundamentals/blockchain"
sidebar_position: 7
---

Blockchain is the foundation of Super Protocol. It provides a decentralized, transparent, and verifiable environment to store information that is critical to the functioning of the system:

- Registered users
- Registered providers
- Available offers, [requirements](/developers/fundamentals/slots), and configuration
- Created orders
- Completed transactions
- Restrictions.

Moreover, the blockchain provides an infrastructure for smart contracts to orchestrate the work of all components, govern system behavior, and process transactions.

Currently, Super Protocol uses the Polygon Amoy testnet. In the future, it will support multiple blockchains simultaneously through a messaging mechanism.

## Smart contracts

The following smart contracts exist in Super Protocol:

- Basic smart contract with the system configuration
- Staking smart contract
- Provider registry smart contract
- TEE Offers smart contract
- Value Offers smart contract
- Orders smart contract
- ERC-20 smart contract.

### Basic smart contract

_Basic smart contract_ contains the information other smart contracts. It is also necessary to update the information about the system and the system settings.

The smart contract contains the following configurations:

#### General smart contract configuration

| **Name** | **Description** |
| :- | :- |
| nextVersionAddress | Address of the newer version of the basic smart contract |
| providerRegistry | Address of the provider smart contract |
| TEEOffers | Address of the TEE offer smart contract |
| valueOffers | Address of the value offer smart contract |
| orders | Address of the order smart contract |
| tokens | Address of the tokens smart contract |

#### Offer configuration

| **Name** | **Description** |
| :- | :- |
| stopDelayDays | Number of days from the moment the offer is declared irrelevant until it is no longer being processed |
| minSecDeposit | Minimum provider security deposit |
| offerSecDeposit | Value offer security deposit |
| offerPenalty | Penalty for not fulfilling a value offer |
| TEEOfferSecDeposit | Compute offer security deposit |
| TEEOfferPenalty | Penalty for not fulfilling a compute offer |
| TEERewardPerEpoch | Reward per epoch (24 hours) for all compute resources |

#### Order configuration

| **Name** | **Description** |
| :- | :- |
| orderMinimumDeposit | Minimum order deposit |
| profitWithdrawDelayDays | Delay in days before a provider can withdraw the reward for a fulfilled offer |

### Staking smart contract

_Staking smart contract_ enables holding tokens for later profit.

The smart contract contains the following records:

| **Name** | **Description** |
| :- | :- |
| owner | Staker name |
| amount | Sum of tokens staked |
| unholdDatetime | Date when the tokens will be released and transferred to the main deposit |

### Provider registry smart contract

_Provider registry smart contract_ enables registration and management of [providers](/developers/fundamentals/offers) in the system.

The smart contract contains the following records:

| **Name** | **Description** |
| :- | :- |
| authority | Authority account address |
| tokenReceiver | Token receiver account address |
| actionAccount | Action account address |
| name | Provider's name |
| description | Provider's description |
| metadata | Metadata with additional information such as a link to a logo |

### Compute offer smart contract

_Compute offer smart contract_ enables the creation and management of [compute offers](/developers/fundamentals/offers#compute). Each compute offers represents a single confidential computing device.

The smart contract contains the following records:

| **Name** | **Description** |
| :- | :- |
| providerAuthority | Authority account address of the offer provider |
| id | Incremental offer ID |
| name | Offer name |
| description | Offer description |
| teeType | Type of the confidential compute: CPU or GPU |
| hardwareInfo | Complete specifications of the device |
| slots | Array of provided slots |
| options | Array of provided options |
| TCBId | TEE Confirmation Block |
| TLB | TEE Loader Block |
| argsPublicKey | Public key and the encryption algorithm for arguments, in JSON format |

### Value offer smart contract

_Value offer smart contract_ enables the creation and management of [value offers](/developers/fundamentals/offers#solution-and-data)—solution, data, and storage offers. Providers can set the price, the minimum order deposit, and the [restriction rules](/developers/fundamentals/offers/#rules) to govern what offers can and cannot do.

The smart contract contains the following records:

- Authority account address of the offer provider
- Offer ID
- Offer group: `INPUT`, `OUTPUT`, or `PROCESSING`
- Offer type: solution, data, or storage
- Offer name
- Offer description
- Other metadata:
    + Flag that indicates if orders that use this offer are cancelable
    + Input and output data format
- Possible restrictions:
    + Offer dependencies and restrictions
    + List of customers allowed to use this offer
- Array of [requrement slots](/developers/fundamentals/slots#requirements)
- Public key and algorithm to encrypt offer arguments.

### Order smart contract

_Order smart contract_ enables the creation of [orders](/developers/fundamentals/orders). The customer who creates an order must consider all restrictions and requirements of all the selected offers and pay a security deposit.

The smart contract determines the minimum deposit for the order based on the sum of minimum deposits of all suborders and the order minimum deposit set by the protocol, whatever is bigger.

The smart contract contains the following records:

- Customer account address
- Compute offer ID
- Order ID
- Lists of public and encrypted arguments
- Order status
- Price and deposits, may change during processing:
    + Current order price
    + Current amount of spent deposit
    + Current amount of deposit spent on options
- Order result:
    + Public key and algorithm to encrypt the order result
    + Encrypted result.