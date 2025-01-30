# Satoshi Governance

A Bitcoin-native decentralized governance protocol built on Stacks, enabling secure and efficient DAO operations with Bitcoin's security guarantees.

## Overview

Satoshi Governance redefines DAOs by anchoring governance to Bitcoin's robust security model. Built on Stacks L2, this protocol provides a comprehensive framework for decentralized decision-making while maintaining the highest standards of security and decentralization.

## Features

### 🔐 Bitcoin-Backed Membership

- Join and leave DAO functionality
- Staking mechanism with STX tokens
- Reputation-based system with time-weighted decay
- Active participation incentives

### 📜 Proposal System

- On-chain proposal creation and management
- Quadratic voting power based on reputation and stake
- Automatic proposal expiration (10 days)
- Treasury-linked proposal execution

### 💫 Reputation System

- Dynamic reputation scoring
- Time-weighted decay for inactive members
- Reputation rewards for positive contributions
- Activity-based reputation adjustments

### 🏦 Treasury Management

- Secure STX token handling
- Donation mechanism
- Balance tracking
- Proposal-based fund distribution

### 🤝 Cross-DAO Collaboration

- Inter-DAO proposal system
- Collaboration status tracking
- Partner DAO verification
- Multi-DAO governance possibilities

## Smart Contract Functions

### Membership Management

```clarity
(define-public (join-dao))
(define-public (leave-dao))
(define-public (stake-tokens (amount uint)))
(define-public (unstake-tokens (amount uint)))
```

### Proposal Management

```clarity
(define-public (create-proposal (title (string-ascii 50))
                              (description (string-utf8 500))
                              (amount uint)))
(define-public (vote-on-proposal (proposal-id uint) (vote bool)))
(define-public (execute-proposal (proposal-id uint)))
```

### Treasury Operations

```clarity
(define-public (donate-to-treasury (amount uint)))
(define-read-only (get-treasury-balance))
```

### Reputation System

```clarity
(define-read-only (get-member-reputation (user principal)))
(define-public (decay-inactive-members))
```

### Cross-DAO Collaboration

```clarity
(define-public (propose-collaboration (partner-dao principal)
                                    (proposal-id uint)))
(define-public (accept-collaboration (collaboration-id uint)))
```

## Error Codes

| Code                            | Description                            |
| ------------------------------- | -------------------------------------- |
| `ERR-NOT-AUTHORIZED` (u100)     | User doesn't have required permissions |
| `ERR-ALREADY-MEMBER` (u101)     | User is already a DAO member           |
| `ERR-NOT-MEMBER` (u102)         | User is not a DAO member               |
| `ERR-INVALID-PROPOSAL` (u103)   | Invalid proposal parameters or state   |
| `ERR-PROPOSAL-EXPIRED` (u104)   | Proposal has expired                   |
| `ERR-ALREADY-VOTED` (u105)      | Member has already voted on proposal   |
| `ERR-INSUFFICIENT-FUNDS` (u106) | Insufficient funds for operation       |
| `ERR-INVALID-AMOUNT` (u107)     | Invalid amount specified               |

## Key Design Features

### Security

- Bitcoin-level security through Stacks integration
- Robust error handling and input validation
- Protected treasury operations
- Time-locked proposals

### Scalability

- Efficient data structures using maps
- Optimized voting mechanisms
- Modular design for future extensions

### Governance

- Quadratic voting power calculation
- Time-weighted reputation system
- Active participation incentives
- Cross-DAO collaboration capabilities

## Implementation Details

### Data Structures

#### Members Map

```clarity
(define-map members principal
  {
    reputation: uint,
    stake: uint,
    last-interaction: uint
  }
)
```

#### Proposals Map

```clarity
(define-map proposals uint
  {
    creator: principal,
    title: (string-ascii 50),
    description: (string-utf8 500),
    amount: uint,
    yes-votes: uint,
    no-votes: uint,
    status: (string-ascii 10),
    created-at: uint,
    expires-at: uint
  }
)
```

### Key Mechanisms

#### Voting Power Calculation

```clarity
(define-private (calculate-voting-power (user principal))
  (let (
    (member-data (unwrap! (map-get? members user) u0))
    (reputation (get reputation member-data))
    (stake (get stake member-data))
  )
    (+ (* reputation u10) stake)
  )
)
```

#### Reputation Updates

```clarity
(define-private (update-member-reputation (user principal) (change int))
  (match (map-get? members user)
    member-data
    (let (
      (new-reputation (to-uint (+ (to-int (get reputation member-data)) change)))
      (updated-data (merge member-data {reputation: new-reputation, last-interaction: stacks-block-height}))
    )
      (map-set members user updated-data)
      (ok new-reputation)
    )
    ERR-NOT-MEMBER
  )
)
```

## Usage Examples

### Creating a New Proposal

```clarity
;; Create a proposal for 1000 STX
(contract-call? .satoshi-governance create-proposal
  "Fund Development"
  "Fund the development of new features"
  u1000)
```

### Voting on a Proposal

```clarity
;; Vote yes on proposal #1
(contract-call? .satoshi-governance vote-on-proposal u1 true)
```

### Staking Tokens

```clarity
;; Stake 500 STX
(contract-call? .satoshi-governance stake-tokens u500)
```

## Best Practices

1. **Proposal Creation**

   - Provide clear, detailed descriptions
   - Request appropriate funding amounts
   - Consider impact on treasury

2. **Voting**

   - Review proposal details thoroughly
   - Consider long-term implications
   - Vote before proposal expiration

3. **Staking**

   - Maintain adequate stake for voting power
   - Consider lock-up period
   - Monitor reputation impacts

4. **Cross-DAO Collaboration**
   - Verify partner DAO credentials
   - Ensure proposal alignment
   - Monitor collaboration status

## Contributing

Contributions to Satoshi Governance are welcome! Please ensure you:

1. Test all changes thoroughly
2. Document new features and changes
3. Follow the existing code style
4. Consider backwards compatibility
5. Include relevant test cases
