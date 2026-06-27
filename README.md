# EVM Token Launch Platform

A Hardhat-based smart contract platform for launching meme tokens on EVM chains. It provides a bonding-curve launchpad with automatic liquidity migration to Uniswap V2–compatible DEX routers.

## Overview

This project implements a FourMeme-style token launch system:

- **FourMemeToken** — minimal ERC-20 token with factory-gated minting
- **PumpCloneFactory** — upgradeable (UUPS) factory for token launches, bonding-curve buy/sell, and Uniswap liquidity migration

Supported networks are configured in `hardhat.config.ts`: Ethereum, Sepolia, BNB Chain (mainnet & testnet), and Monad testnet.

## Project Structure

```
contracts/
  FourMemeToken.sol      # ERC-20 token implementation
  FourMemeFactory.sol    # Upgradeable launchpad factory (PumpCloneFactory)
scripts/
  deployPump.ts          # Deploy token impl + UUPS factory proxy
  util.ts                # Verification and deployment helpers
hardhat.config.ts        # Network and compiler configuration
```

## Prerequisites

- [Node.js](https://nodejs.org/) 18+
- A wallet private key for deployment (never commit this)

## Setup

```bash
npm install
```

Create a `.env` file in the project root:

```env
PRIVATE_KEY=your_wallet_private_key
```

## Compile

```bash
npx hardhat compile
```

## Deploy

Deploy to the default Hardhat network:

```bash
npx hardhat run scripts/deployPump.ts
```

Deploy to a specific network:

```bash
npx hardhat run scripts/deployPump.ts --network sepolia
npx hardhat run scripts/deployPump.ts --network bnbtestnet
npx hardhat run scripts/deployPump.ts --network monad
```

The deployment script:

1. Deploys a `FourMemeToken` implementation
2. Deploys a UUPS proxy for the factory with the Uniswap V2 router and token implementation addresses

Update the `routerAddress` in `scripts/deployPump.ts` before deploying to mainnet.

## Networks

| Network      | Chain ID | RPC (configured)                    |
|--------------|----------|-------------------------------------|
| Ethereum     | 1        | publicnode.com                      |
| Sepolia      | 11155111 | Infura                              |
| BNB Mainnet  | 56       | bsc-mainnet.publicnode.com          |
| BNB Testnet  | 97       | bsc-testnet.publicnode.com          |
| Monad Testnet| 10143    | testnet-rpc.monad.xyz               |

## Contracts

### FourMemeToken

Standard ERC-20 with `transfer`, `approve`, and `transferFrom`. Only the factory can call `mintFromFactory`.

### PumpCloneFactory

Upgradeable factory (`FourMemeFactory.sol`) that manages:

- Token launches via minimal proxy clones
- Bonding-curve buy and sell before migration
- Liquidity migration to Uniswap V2 when the curve completes
- Owner-configurable reserves, fees, and treasury settings

## License

MIT (factory contract). See individual contract headers for details.
