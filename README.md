# Achor Core Staking

Anchor program for staking MPL Core NFTs.  
Built with `anchor 0.31.x` and `mpl-core 0.11.x`.

## What it does  

- Creates/initializes a stake-ready collection (`create_collection`, `initialize`).
- Mints NFTs into that collection (`mint_asset`).
- Configures per-collection staking parameters: reward basis-points and freeze period (`initialize`).
- Stakes an NFT by freezing the asset and stamping `staked=true` plus `staked_at` attributes (`stake`).
- Unstakes after the freeze window, calculates time-based rewards, and mints a `rewards_mint` SPL token to the owner (`unstake`).

## Prerequisites

- Rust toolchain (`rustup`, `cargo`)
- Solana CLI (`solana-install init`, etc.)
- Anchor CLI **matching** the `anchor-lang` crate version (currently `0.31.1`)  
  > Install with `npm i -g @coral-xyz/anchor@0.31.1` so you don’t hit localnet TS errors.

## Quick start

```bash
# Install deps
yarn install

# Build the program
anchor build

# Run tests (needs a local validator)
anchor localnet
anchor test

# Or deploy to a remote cluster
# In Anchor.toml set cluster = "devnet" (or "mainnet-beta"), then:
anchor deploy
```

## Project layout

```
programs/achor-core-staking/
├── src/
│   ├── instructions/
│   │   ├── create_collections.rs
│   │   ├── initialize.rs
│   │   ├── mint_asset.rs
│   │   ├── stake.rs
│   │   └── unstake.rs
│   ├── state/config.rs
│   ├── error.rs
│   └── lib.rs
└── tests/anchor-core-staking.ts

migrations/deploy.ts
```

## Key types (IDL paths)

- `create_collection` — seeds `update_authority` + `collection`
- `initialize` — creates `config` and `rewards_mint` PDAs
- `mint_asset` — creates an NFT under the collection
- `stake` / `unstake` — freeze/unfreeze + reward mint CPI

## Common issues

- **`anchor-lang` vs CLI version mismatch** — pin `@coral-xyz/anchor` in package.json and install it globally at the same version.
- **`Object literal may only specify known properties`** — the TS test uses `owner` (not `user`) for `Stake`/`Unstake`.
- **`Cannot find module '@metaplex-foundation/umi'` during tsc** — add `"skipLibCheck": true` in `tsconfig.json`.

```json
{
  "compilerOptions": {
    "types": ["mocha", "chai"],
    "typeRoots": ["./node_modules/@types"],
    "lib": ["es2015"],
    "module": "commonjs",
    "target": "es6",
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

## Scripts

```bash
yarn lint       # prettier --check
yarn lint:fix   # prettier -w
```
