# SmartWealth Protocol - Automated Portfolio Management on Stacks L2

[![Clarity Version](https://img.shields.io/badge/Clarity-2.12.0+-blue)](https://docs.stacks.co/docs/clarity/)

Enterprise-grade DeFi protocol for automated, non-custodial portfolio management with Bitcoin settlement finality.

## Overview

SmartWealth is a decentralized portfolio management system enabling:

- Multi-asset portfolios (up to 10 tokens)
- Programmatic rebalancing (24h intervals)
- Institutional-grade asset allocation (0.01% granularity)
- Transparent fee structure (0.25% protocol fee)
- Bitcoin-compliant operations via Stacks L2

## Key Features

1. **Non-Custodial Architecture**  
   Users retain full asset custody with portfolio rules enforced by smart contracts

2. **Precision Allocation Engine**  
   Target allocations specified in basis points (1 = 0.01%) with automated drift correction

3. **Multi-Token Support**  
   Native integration with STX, SIP-010 tokens, and Bitcoin-wrapped assets

4. **Institutional Security Model**  
   Formal verification of core logic with Clarity's inherent security guarantees

5. **Regulatory Compliance**  
   Audit-friendly design with transparent portfolio state transitions

## Contract Architecture

### Core Components

| Component         | Description                                  |
| ----------------- | -------------------------------------------- |
| `Portfolios`      | Master registry of all portfolio metadata    |
| `PortfolioAssets` | Asset allocation records per portfolio       |
| `UserPortfolios`  | User->Portfolio mapping (20 portfolio limit) |
| `protocol-fee`    | Protocol revenue parameter (25 basis points) |

### System Constants

| Constant                   | Value | Description                      |
| -------------------------- | ----- | -------------------------------- |
| `MAX-TOKENS-PER-PORTFOLIO` | 10    | Maximum supported assets         |
| `BASIS-POINTS`             | 10000 | Percentage precision (1 = 0.01%) |
| `REBALANCE_INTERVAL`       | 144   | Blocks between rebalances (~24h) |

## Getting Started

### Prerequisites

- [Clarinet](https://docs.hiro.so/clarinet) v2.12.0+
- Node.js 18.x
- Stacks.js SDK

### Usage Patterns

**1. Portfolio Creation**

```clarity
(create-portfolio
   (list 'ST1HTBVD3JG9C05J7HBJTHGR0GGW7KXW28M5JS8QE.token-a
         'ST1HTBVD3JG9C05J7HBJTHGR0GGW7KXW28M5JS8QE.token-b)
   (list u3000 u7000))  ;; 30% / 70% allocation
```

**2. Portfolio Rebalancing**

```clarity
(rebalance-portfolio u123)  ;; Portfolio ID
```

**3. Allocation Update**

```clarity
(update-portfolio-allocation
   u123  ;; Portfolio ID
   u1    ;; Token index
   u2500)  ;; New 25% allocation
```

## API Reference

### Core Functions

#### `create-portfolio`

```clarity
(define-public (create-portfolio (initial-tokens (list 10 principal)) (percentages (list 10 uint)))
```

- `initial-tokens`: List of token contract addresses
- `percentages`: Initial allocations in basis points (sum must = 10000)
- Returns: New portfolio ID (uint)

#### `rebalance-portfolio`

```clarity
(define-public (rebalance-portfolio (portfolio-id uint))
```

- Triggers portfolio rebalancing if outside 24h window
- Updates `last-rebalanced` timestamp

### Query Methods

#### `get-portfolio`

```clarity
(define-read-only (get-portfolio (portfolio-id uint))
```

Returns full portfolio metadata:

- Owner principal
- Creation/rebalance timestamps
- Total value (USD-denominated)
- Active status
- Token count

## Error Handling

| Code     | Description                   |
| -------- | ----------------------------- |
| ERR-U100 | Unauthorized operation        |
| ERR-U101 | Invalid portfolio ID          |
| ERR-U106 | Invalid allocation percentage |
| ERR-U107 | Maximum token limit exceeded  |
| ERR-U110 | Invalid token index           |

## Security Model

1. **Formal Verification**
   All core logic formally verified using Clarity's inherent safety properties

2. **Time-Locked Rebalancing**
   Mandatory 24h cooldown between rebalance operations

3. **Asset Isolation**
   No direct token custody - portfolio rules enforced via smart contracts

4. **Governance Safeguards**
   Protocol parameters controlled through multi-sig ownership
