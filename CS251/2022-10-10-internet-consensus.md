---
math: true
---

# Consensus on the internet

Characterized by open participation:
* Adversary can create many Sybil nodes to try to take care of the protocol
* Honest participants come and go at will

Goals:
* Limit adversary's participation - Sybil resistance
* Maintain availability (liveness) of protocol against fluctuating participation by honest nodes - dynamic availability

## Sybil-resistant protocols

* How to select nodes to participate in consensus?
    - Permissioned: fixed set of nodes (e.g., previous lecture)
    - Permissionless: anyone satisfying certain criteria can participate
        - However, we can't accept anyone with a signing key: sybil attack
* Sybil attack: single node pretends to be multiple fake identities to gain network influence
* Example sybil-resistant protocols:
    - Proof-of-work: computational power dedicated to protocol; e.g. Bitcoin, Ethereum (pre-Sep. 2022)
    - Proof-of-stake: total number of coins dedicated to protocol; e.g. Algorand, Cardano, Ethereum (since Sep. 2022)
    - Proof-of-space/time: total storage across time dedicated to protocol; e.g., Chia, Filecoin

### Bitcoin consensus

* To mine new block, miner must find nonce such that `H(h_prev, txn_root, nonce) < 2^256/D`
    - `D` difficulty: how many nonces on average do miners try until finding a block?
* Each miner tries different nonces until one finds a nonce that satisfies above equation

### Nakamoto consensus

* Fork-choice/proposal rule: at any given round, each miner attemps to extend (i.e., mine tip of) longest chain its view, ties broken adversarially
* Confirmation rule: each miner confirms block (along w/ prefix) that is $k$-deep with longest chain in view
* Leader-selection rule: proof-of-work

### Security for Bitcoin

* Show that Bitcoin is secure under synchrony against Byzantine adversary
* Best possible resistance: $\beta < 1/2$
    - Thm: If $\beta < 1/2$, then there exists a $\lambda(\delta, \beta)$ where Bitcoin satisfies security except with probability $e^{-\Omega(k)}$ under synchronous attack