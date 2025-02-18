# sBTC Options Protocol

A secure, decentralized protocol for Bitcoin options trading on Stacks, leveraging sBTC for seamless Bitcoin integration. This protocol enables trustless options trading with automated settlement, robust price oracle integration, and comprehensive risk management.

## Overview

The sBTC Options Protocol is a decentralized financial instrument that allows users to trade Bitcoin options in a trustless manner on the Stacks blockchain. It utilizes sBTC (a Bitcoin wrapper on Stacks) for collateral and settlement, providing a secure and efficient way to participate in options trading.

## Features

- **Decentralized Options Trading**: Create and trade Bitcoin CALL and PUT options without intermediaries
- **sBTC Integration**: Seamless integration with Bitcoin through sBTC for collateral and settlement
- **Automated Settlement**: Smart contract-based settlement process
- **Price Oracle Integration**: Reliable price feeds with validity window checks
- **Comprehensive Risk Management**: Collateral ratio enforcement and balance tracking
- **Flexible Parameters**: Configurable fees, collateral ratios, and validity windows

## Contract Functions

### Core Trading Functions

#### `deposit-sbtc`

- Deposit sBTC as collateral or trading balance
- Parameters:
  - `amount`: Amount of sBTC to deposit (must be between MIN_DEPOSIT_AMOUNT and MAX_DEPOSIT_AMOUNT)

#### `create-option`

- Create a new options contract
- Parameters:
  - `option-type`: "CALL" or "PUT"
  - `strike-price`: Target price for the option
  - `expiry`: Block height at which the option expires
  - `amount`: Size of the option contract

#### `exercise-option`

- Exercise an active option contract
- Parameters:
  - `option-id`: Unique identifier of the option

#### `expire-option`

- Expire an option and return collateral
- Parameters:
  - `option-id`: Unique identifier of the option

### Oracle Functions

#### `update-btc-price`

- Update the current BTC price (oracle only)
- Parameters:
  - `new-price`: Updated BTC price

#### `get-current-btc-price`

- Retrieve the current valid BTC price
- Returns the current price if within validity window

### Administrative Functions

#### `set-platform-fee`

- Update the platform fee (owner only)
- Parameters:
  - `new-fee`: New fee in basis points (0-10000)

#### `set-min-collateral-ratio`

- Update minimum collateral ratio (owner only)
- Parameters:
  - `new-ratio`: New ratio (100-1000)

#### `set-oracle-address`

- Update the price oracle address (owner only)
- Parameters:
  - `new-oracle`: Principal of the new oracle

#### `set-price-validity-window`

- Update price validity window (owner only)
- Parameters:
  - `new-window`: New window in blocks (10-1440)

## System Parameters

### Limits and Constraints

- **Maximum Fee**: 100% (10000 basis points)
- **Collateral Ratio Range**: 100% to 1000%
- **Minimum Deposit**: 1000 sBTC units
- **Maximum Deposit**: 100,000,000,000 sBTC units
- **Price Validity**: 10 to 1440 blocks
- **Default Collateral Ratio**: 150%
- **Default Platform Fee**: 0.1% (10 basis points)

### Error Codes

| Code | Description             |
| ---- | ----------------------- |
| 100  | Not authorized          |
| 101  | Invalid amount          |
| 102  | Insufficient balance    |
| 103  | Option not found        |
| 104  | Option expired          |
| 105  | Invalid strike price    |
| 106  | Invalid expiry          |
| 107  | Insufficient collateral |
| 108  | Option not exercisable  |
| 109  | Stale price             |
| 110  | Invalid price           |
| 111  | Option not expired      |
| 112  | Invalid parameter       |

## Data Structures

### Option Contract

```clarity
{
    creator: principal,
    holder: principal,
    option-type: (string-ascii 4),
    strike-price: uint,
    expiry: uint,
    amount: uint,
    collateral: uint,
    status: (string-ascii 10)
}
```

### User Balance

```clarity
{
    sbtc-balance: uint,
    locked-collateral: uint
}
```

## Security Features

1. **Access Control**

   - Contract owner privileges for administrative functions
   - Oracle authorization for price updates
   - User authorization for option operations

2. **Balance Protection**

   - Strict balance checking
   - Collateral locking mechanism
   - Safe arithmetic operations

3. **Price Security**

   - Price validity window
   - Stale price protection
   - Zero/null price prevention

4. **Option Safety**
   - Expiry validation
   - Status state machine
   - Collateral ratio enforcement

## Integration Guide

### Implementing an Oracle

1. Deploy an oracle contract with the following function:

```clarity
(define-public (update-price (new-price uint))
    (contract-call? .bitcoin-options update-btc-price new-price))
```

2. Set the oracle address using `set-oracle-address`
3. Ensure regular price updates within the validity window

### Creating Options

1. Deposit sBTC using `deposit-sbtc`
2. Calculate required collateral (amount \* strike-price / 100)
3. Create option with `create-option`
4. Monitor option status through `get-option`
