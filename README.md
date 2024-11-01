### Contact me

Gmail : maniveer198@gmail.com

Github : [Maniveer007](https://github.com/Maniveer007)

NOTE : to make this project execute to idustry standards we need to maintain unique address of registry contract on different chains which we can achieve with axelar create2 address but it doesnt support Avalanche C-Chain and Manta Pacific thats the main reason why we havent deployed our contracts on those chains

### Overview

Basic ERC-6551 enables Token Bound Accounts on same chain
our solution CrossChain ERC6551 utilize true potential of Reactive Network and Makes a drastic change in history of TokenBoundedAccounts

[![flowchart.jpg](https://i.postimg.cc/yNmf6SZL/flowchart.jpg)](https://postimg.cc/Ny08pFbX)

## **LINKS**

frontend : [https://reactivecrosschainerc6551.vercel.app/](https://reactivecrosschainerc6551.vercel.app/)

ERC6551_Origin : [Deployment link](https://sepolia.etherscan.io/tx/0xb460635406cf56488b5ce880e4b6904f87e2fafdfa5fa222a7693b1e8c0dd8bb)

ERC6551_Registry : [Depolyment link](https://sepolia.etherscan.io/tx/0x416ca6326a39281948d7e54246a2f5cf38eb62dc2fddb3ad505fb4339332f75f)

### The Challenge

Traditional TBAs(TokenBoundedAccounts) are limited to a single blockchain, hindering their potential and restricting their utility. Cross-chain interoperability has been a significant challenge, with existing solutions often complex, costly, and inefficient.

### Description of our solution

we are taking ERC6551 to next step by introducing crosschain account where can create `ERC6551Account` on different chains

- **Creating cross-chain TBAs**: Easily generate TBAs that can exist and function across multiple blockchains.
- **Enhanced NFT utility**: Expand the capabilities of NFTs by allowing them to own, manage, and interact with assets on different chains.
- **Improved security**: Leverage the security benefits of TBAs while extending them to a cross-chain environment.

### Flow of how CrossChain ERC6551 works

- when user calls function `createAccount` on origin chain the contract checks where the user is owner of Token or not if the user is owner of token then it emits `CreateAccount` event

- on Reactive Network our `ERC6551_Reactive` contract is already subscribed to `ERC6551_origin` contract when an `CreateAccount` event is emitted from origin contract it sends a call to callback function `createAccount` to `ERC6551_Registry` on destination chain and subscribe to events of the `Token` for which account is created (so that when ownership of token changes it changes the ownership of Account accordingly)

- when reactive `CALLBACK_SENDER` triggers createAccount function on `ERC6551_Registry`(destination chain) it creates `ERC6551 Account` for the owner of Token on origin chain

[![flowchart.jpg](https://i.postimg.cc/cHL38cZH/flowchart-8.png)](https://postimg.cc/p93rwzmb)

[![flowchart.jpg](https://i.postimg.cc/Pq4ZxRg9/flowchart-9.png)](https://postimg.cc/7C5btmYn)

- when reactive `CALLBACK_SENDER` triggers changeOwnerofAccount function on `ERC6551_Registry`(destination chain) it changes ownership of Account to the new owner of token(this is triggered when ownership is changes on origin chain)

### Importance of Reactive network

A reactive network is crucial for your project due to its ability to listen to blockchain events and trigger transactions based on predefined logic. Here's a detailed explanation of its importance:

- **Event-Driven Architecture**: The reactive network enables an event-driven architecture, which is essential for responding to specific blockchain events. In your project, it listens to events such as createAccount and transfer on the origin chain.

- **Cross-Chain Operations**: By listening to these events, the reactive network facilitates cross-chain operations. When a user has a token on the origin chain and triggers the createAccount function, the reactive network detects this event and creates an account on the destination chain.

- **Dynamic Ownership Management**: The reactive network also listens to ownership transfer events. When the ownership of a token changes, it updates the ownership of the associated account on the destination chain to reflect the new owner. This dynamic management ensures that the accounts are always up-to-date with the current token owners.

- **Automation and Efficiency**: The reactive network automates the entire process, reducing the need for manual interventions. This automation not only enhances efficiency but also ensures that the operations are executed in real-time, providing a seamless user experience.

- **Scalability and Flexibility**: With a reactive network, the system can scale efficiently, handling multiple events and transactions across different chains simultaneously. Its flexible nature allows easy integration of additional functionalities or support for more chains in the future.

#### **Without Reactive Network**

Without the reactive network, achieving the same level of functionality and efficiency in your project would be highly challenging, if not impossible. Here's why:

- **Manual Monitoring and Execution**: Without the reactive network, the process of monitoring events and executing corresponding transactions would require manual intervention. This not only increases the risk of errors but also significantly slows down the entire process.

- **Delayed Updates**: The absence of a reactive network would mean that updates to account ownership and other critical operations would not be instantaneous. This delay can lead to inconsistencies and a poor user experience.

- **Complex Cross-Chain Management**: Managing cross-chain interactions manually would be complex and error-prone. The reactive network simplifies this by automatically handling event detection and transaction execution across chains.

- **Scalability Issues**: Without automation, scaling the system to handle numerous events and transactions across multiple chains would be impractical. The reactive network provides the necessary infrastructure to scale efficiently.

There are four main contracts involved in this scenario:

- ERC6551_origin contract.
- ERC6551_Reactive contract.
- ERC6551_Registry contract.
- ERC6551Account contract

## ERC6551_origin contract

The `ERC6551_origin` contract on origin chain where users have their NFT and the deployer of ERC6551_origin has ERC_6551Admin role and

onlyAdmin can `add/remove` supported destination chain because as of now Reactive network is only avaliable on `Sepolia` if Reactive network supports all destination chains we can remove ERC_6551Admin role

when ever user wants to create TBA(Token Bounded Account) crosschain the user calls function `createAccount` with supported destination chain and token details this function emits `CreateAccount` event

## ERC6551_Reactive contract.

The `ERC6551_Reactive contract` is an advanced example of a reactive contract designed for use with `ERC6551_Origin` and the Reactive Network. This contract subscribes to events from both a `ERC6551_Origin` and NFT's for which `ERC6551_Registry` created TBA(TokenBoundedAccount).

The constructor sets up the initial state, including the reactive network subscription to the `ERC6551_Origin`. It stores addresse of `ERC6551_Registry` as callback.

The `react` function is the core of the contract, which processes events from the reactive network.

- when `createAccount` event emitted from `ERC6551_Origin` on origin chain it sends a `createAccount` function call to `ERC6551_Registry` to create TBA(TokenBoundedAccount) for the respective NFT and then subscribes to events recieved from the Tokencontract to tract the ownership of NFT and make sure the Account owner will be the owner if NFT (Which is main principle of TBA)

- when `transfer` event is emmited from Token on Origin chain reactive network triggers `react`function which sends a callback to change the owner of `crosschain TBA`

## ERC6551_Registry contract

`ERC6551_Registry` is registry contract for crosschain TBA(TokenBoundedAccount) when reactive networks sends callback to create crosschain account it triggers `createAccount` function note it can be only called by reactive network callback sender and sets the owner of account as owner of token

when ever Token ownership change in origin chain the reactive network triggers a callback calling `changeOwnerofAccount` function to change the ownership of account also

## ERC6551Account contract

this is implementation of Tokenbounded account is inspired from Team TokenBoundOrg
this is implementation of smartcontract account

## Deployment & Testing

To deploy testnet contracts to Sepolia, follow these steps, making sure you substitute the appropriate keys, addresses, and endpoints where necessary. You will need the following environment variables configured appropriately to follow this script:

```
export SEPOLIA_RPC=
export SEPOLIA_ERC6551ADMIN_PRIVATE_KEY=
export SEPOLIA_USER_PRIVATE_KEY=
export REACTIVE_RPC=
export REACTIVE_PRIVATE_KEY=
export SYSTEM_CONTRACT_ADDR=
export CALLBACK_SENDER_ADDR=
export USER_PUBLIC_KEY=
export ERC721_CONTRACT_ADDR=
export ERC721_TOKEN_ID=
```

### Step 1

`ERC6551_ADMIN` will Deploy the `ERC6551_origin contract` (origin chain contract) and assign the `Deployed to` address from the response to `ORIGIN_ADDR`.

```bash
forge create --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_ERC6551ADMIN_PRIVATE_KEY src/ERC6551/ERC6551_origin.sol:ERC6551_origin --legacy
```

Proof of Output :

```
Deployer: 0xAFE08919dAC82E79ae274eb94441AA2447Bb13b6
Deployed to: 0xff99b78beB039e9831664FE4ebef3F23e9a50EF6
Transaction hash: 0xb460635406cf56488b5ce880e4b6904f87e2fafdfa5fa222a7693b1e8c0dd8bb
```

transaction hash :[0xb460635406cf56488b5ce880e4b6904f87e2fafdfa5fa222a7693b1e8c0dd8bb](https://sepolia.etherscan.io/tx/0xb460635406cf56488b5ce880e4b6904f87e2fafdfa5fa222a7693b1e8c0dd8bb)

export ORIGIN_ADDR=0xff99b78beB039e9831664FE4ebef3F23e9a50EF6

### Step 2

Deploy the `ERC6551_Registry contract` (destination chain contract) and assign the `Deployed to` address from the response to `DESTINATION_ADDR`.

```bash
forge create --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_ERC6551ADMIN_PRIVATE_KEY src/ERC6551/ERC6551_Registry.sol:ERC6551_Registry --constructor-args $CALLBACK_SENDER_ADDR --value 0.05 ether --legacy
```

Proof of Output :

```
Deployer: 0xAFE08919dAC82E79ae274eb94441AA2447Bb13b6
Deployed to: 0xB1B79De78A1700813fc72798Ee893E4FA9Fa6AAb
Transaction hash: 0x416ca6326a39281948d7e54246a2f5cf38eb62dc2fddb3ad505fb4339332f75f
```

transaction hash :[0x416ca6326a39281948d7e54246a2f5cf38eb62dc2fddb3ad505fb4339332f75f](https://sepolia.etherscan.io/tx/0x416ca6326a39281948d7e54246a2f5cf38eb62dc2fddb3ad505fb4339332f75f)

export DESTINATION_ADDR=0xB1B79De78A1700813fc72798Ee893E4FA9Fa6AAb

### Step 3

Deploy the `ERC6551_Reactive` (reactive contract), configuring it to listen to `ORIGIN_ADDR` and to send callbacks to `DESTINATION_ADDR`.

```bash
forge create --rpc-url $REACTIVE_RPC --private-key $REACTIVE_PRIVATE_KEY src/ERC6551/ERC6551_Reactive.sol:ERC6551_Reactive --constructor-args $SYSTEM_CONTRACT_ADDR $ORIGIN_ADDR $DESTINATION_ADDR [11155111] --legacy
```

Proof of Output :

```
Deployer: 0xAFE08919dAC82E79ae274eb94441AA2447Bb13b6
Deployed to: 0x33D06347F4083D3cae7B9Cc0DeD37fe45dA3409E
Transaction hash: 0x0796f97e0fa3ec37c4b855b8f2276835db6df98aaf72be923a51a5f297c12d69
```

### Step 4

adding sepolia as destination chain by `ERC6551_ADMIN`

```bash
cast send $ORIGIN_ADDR 'addNewChain(uint256)' --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_ERC6551ADMIN_PRIVATE_KEY 11155111 --legacy
```

Proof of Output :
Transaction Hash :[0x6932db4776582b45bee629cafa20b7e94c6331e0eab37af0fc8927681f85ea43](https://sepolia.etherscan.io/tx/0x6932db4776582b45bee629cafa20b7e94c6331e0eab37af0fc8927681f85ea43)

### Step 5

Test the `CreateAccount` function in `ERC6551_origin contract`:

```bash
cast send $ORIGIN_ADDR 'createAccount(uint256,address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_USER_PRIVATE_KEY 11155111 $ERC721_CONTRACT_ADDR $ERC721_TOKEN_ID --legacy
```

this step will deploy Account for token owner on `ERC6551_Registry contract (Destination chain)` and subscribe for token Transfer event when ever token ownership changes it automatically changes the owner

Proof of Output :

Transaction hash for createAccount function :[0x9ba56eb226af8ab992edbed94c5d7c63d23a0aaf9099883619cff1c76d20b99e](https://sepolia.etherscan.io/tx/0x9ba56eb226af8ab992edbed94c5d7c63d23a0aaf9099883619cff1c76d20b99e)

reactive Network called createAccount function on destination chain TX hash: [0x1082192adbf5a25b793ec422774bcbe0422874f711ca6cc8fec4cd66b09560c9](https://sepolia.etherscan.io/tx/0x1082192adbf5a25b793ec422774bcbe0422874f711ca6cc8fec4cd66b09560c9)

if ownership of token changes automatically reactive network changes ownership of account as account is subjected to token (TokenBoundedAccount)

```bash
cast send $ERC721_CONTRACT_ADDR 'transferFrom(address,address,uint256)' --rpc-url $SEPOLIA_RPC --private-key $SEPOLIA_USER_PRIVATE_KEY $USER_PUBLIC_KEY <anyAddress> $ERC721_TOKEN_ID --legacy
```

Transaction Hash of transferfrom function :[0x7799a3c511cc779e421cc4ace3ea5b32a2a16f6fa9c9a51f3ae0f5cacf4b8e95](https://sepolia.etherscan.io/tx/0x7799a3c511cc779e421cc4ace3ea5b32a2a16f6fa9c9a51f3ae0f5cacf4b8e95)

reactive network triggered `changeOwnerofAccount` function on destination chain TX Hash :[0x1082192adbf5a25b793ec422774bcbe0422874f711ca6cc8fec4cd66b09560c9](https://sepolia.etherscan.io/tx/0x1082192adbf5a25b793ec422774bcbe0422874f711ca6cc8fec4cd66b09560c9)
