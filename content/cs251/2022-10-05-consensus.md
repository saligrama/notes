---
math: true
---

# Fundamentals of Consensus

## Byzantine Generals Problem (Lamport et al., 1982)

* N (fixed) generals, one is commander
* Some generals are loyal, some are traitors (incl. commander)
* Commander sends out order to *attack* or *retreat*
    - Commander is loyal: send out same order to all generals
    - Commander is traitor: sends out different orders to confuse
* Goal: all loyal generals should take the same action
    - Which should be the one issued by the commander, if commander is loyal

## Generalized consensus problem

* Solution to a consensus problem is a *consensus protocol*
* To generalize Byzantine Generals Probelm: generals are *nodes*, the commander is the *leader*, loyal generals are *honest* nodes, traitors are *the adversary*

### The adversary

* Role of an adversary: *corrupt* nodes, making them *adversarial*
* Types of adversaries:
    - Induces *crash faults* if the adversarial nodes do not send or receive any messages
    - Induces *omission faults* if the adversarial nodes can selectively choose to drop or let through messages sent or received
        - Note: omission fault adv. can emulate crash fault adv. in all cases => stronger adversary
    - Byzantine faults (Byzantine adversary): adversarial nodes can deviate from protocol arbitrarily
* Typical assumption: adversary cannot forge signatures
* Adaptive vs. static adversary:
    - Static adversary: corrupts nodes of its choice pre-protocol execution
    - Adaptive adversary: can dynamically corrupt nodes during protocol execution
* Bounds on adversary's power:
    - Assume upper bound on number of nodes $f$ that can be adversarial, as a fraction of the $n$ nodes

### Communication

* Nodes can send messages to each other within the protocol
* Time proceeds in discrete *rounds*
* Adversary controls delivery of messages, with limits:
    - Synchronous network: adversary must deliver messages sent by honest nodes to recipients within $d$ rounds
    - Async. network: adversary can delay any message for arbitrary, but finite, number of rounds
    - Partial synchrony: exists a known $t$ and event called Global Stabilization Time (GST) where
        - GST eventually happens after some finite time that can be chosen arbitrarily by adversary
        - Message sent by honest node at round $t$ is delivered to recipient by round $d + max(GST, t)$
        - In effect, network async. until GST, after which it behaves like synchronous

## State machine replication (SMR)

* Theoretically, could have centralized bank, but want to decentralize
* SMR participants:
    - Replicas: receive transactions, execute SMR protocol
    - Clients: learners, each outputs a log
        - Tries to learn what correct ledger should be
* Goal: ensure clients output same logs
    - Compared to Byzantine Generals Problem: this is multi-shot: log is continuously output, rather than single value output
    - Also: learners (log output) are separate from nodes executing protocol
* e.g. Replicas $r_1 \ldots r_5$, Clients $c_1 \ldots c_4$
    - Replicas receive transactions $tx_1 \ldots tx_4$
    - Clients ask replicas what log sequence should be

### Security for SMR

* Let $LOG_t^{i}$ be the log ouptut by client $i$ at round $t$
* Secure SMR protocol guarantees:
    - Safety (consistency): for clients $i, j$, times $t, s$: $LOG_t^{i}$ should be a prefix of $LOG_s^{j}$, or vice versa
    - Liveness: if a transaction $tx$ is output to a honest replica at some time $t$, then for all clients $i$, times $s \ge t + T_{conf}$, then $tx \in LOG_s^{i}$

### Baby streamlet

* Time: epochs of $2d$ rounds
    - For each epoch $e$, leader $L_e$ chosen by public hash function $H$
    - Blocks: each block is associated with an epoch
* $n$ replicas, fixed before protocol execuction, every replica knows all other replica public keys (creating authenticated communication channels)
* At each epoch $e = 1, 2, \ldots$:
    - Propose: at start of epoch $e$, $L_e$ identifies longest seen chain and proposes new block extending that chain
    - Finalization rule: client finalizes block (and prefix) at tip of longest chain; tiebreak by smaller epoch
* Proving security under $f < n/3$, partial synchrony, Byzantine adversary:
    - Safety: no two blocks can be finalized at the same height
        - However, with adversarial leader and pre-GST:
            - Adv. sends $B_1$ to Alice, which gets notarized
            - Adv. sends $B_2$ to Bob, which gets notarized
            - Both are plopped on top of $B_1$ at the same time, which is not safe
        - Therefore, Baby Streamlet is not safe (so not secure)!

### Teen streamlet

* Setup: same as baby streamlet
    - Additionally: votes: vote on block by a replica is its signature on the block
    - Notarization: block *notarized* in view of replica or client if observed over $2n/3$ signatures from distinct replicas on the block
* At each epoch, in adition to propose:
    - Vote: $d$ rounds into epoch $e$: each honest replica *votes* for first valid epoch $e$ proposal from $L_e$ that extends longest notarized chain in its view. If no such block, no vote
    - Finalization rule: client finalizes block and prefix once observed notarization of block
* Proving security under same constraints:
    * Safety: now works under constraints, because cannot get over $2n/3$ votes for duplicate notarization with only adversarial votes