# Bitcoin mechanics

## Overview of the Bitcoin consensus layer

* Miners broadcast received transaction (Tx) to peer-to-peer (P2P) network
* Every miner validates received Tx and stores them in its **mempool** (unconfirmed Tx set)
    - Implication: miners see all Tx before posted on chain
* Every ~10 minutes:
    - Each miner creates a candidate block from Tx in its mempool
    - A "random" miner is selected and broadcasts its block to P2P network
    - Selected miner is paid 6.25 BTC in **coinbase Tx** (first Tx in the block)
        - Only way new BTC is created
        - Block reward halves every four years
            - Max 21M BTC (currently at 19.1M BTC)
        - Economically: this inflates the currency - more supply means each individual BTC gets slightly devalued
    - Note: miner chooses order of Tx in block

## The bitcoin blockchain

### Block headers

* Blockchain: a sequence of block headers, 80 bytes each
    - Fields: 
        - version (4b)
        - prev (link to prev. block) (32b)
        - time - self-report of time miner assembled block (4b)
        - bits - PoW difficulty (4b)
        - nonce - PoW solution (4b)
        - Merkle root - payer can give short proof Tx is in the block
    - New block every ~10 min
* This lecture: view blockchain as Tx sequence (append only)

### Transaction structure (non-coinbase)

* Inputs: assets going into transaction
    - `TxID` - 32b hash
        - equal to `H(Tx)` (hash)
    - `out-index` - 4b index
    - `ScriptSig` - program
    - `seq` - ignore
* (segwit) Witnesses: part of input
* outputs: who gets funds?
    - `value` - 8b
        - specified in Satoshis
        - #BTC = value/10<sup>8</sup>
    - `ScriptPK`- program
* locktime: earliest block number that can include Tx

### Validating a transaction

* Miners check for each input:
    1. The program `ScriptSig | ScriptPK` (concatenation) returns true
    2. `TxID | index` is in the current UTXO (unspent transaction output) set
    3. Sum input values >= sum output values
* Spend UTXO - remove from UTXO set in memory

### Bitcoin script

* A stack machine
* Not Turing complete: no loops
* Op codes:
    1. `OP_TRUE` or `(OP_1)` (81), `OP_2` (82), ..., `OP_16` (96)
    2. `OP_DUP` (118): push top of stack onto stack
    3. Control
        - (99) `OP_IF` \<statements\>, `OP_ELSE` \<statements\>, `OP_ENDIF`
        - (105) `OP_VERIFY`: abort fail if `top = false`
        - (106) `OP_RETURN`: abort and fail   
            - for `ScriptPK = [OP_RETURN, <data>]` where `<data>` is not a script
        - (136) `OP_VERIFY`: pop, pop, abort fail if pops not equal
    4. Arithmetic
        - `OP_ADD`, `OP_SUB`, `OP_AND`, ...: pop two items, operate, push
    5. Crypto
        - `OP_SHA256`: pop, hash, push
        - `OP_CHECKSIG`: pop sig, pop pk, verify sig. on TX, push 0 or 1
    6. Time: `OP_CheckLockTimeVerify` (CLTV)
        - fail if value at top of stack > Tx locktime value
        - usage: UTXO can specify min time where it can be spent

### Transaction types

1. P2PKH: pay to public key hash
    - e.g. Alice wants to pay Bob 5 BTC
        1. Bob generates sig key pair `(pk_b, sk_b) <- Gen()`
        2. Bob computes bitcoin addr `addr_b <- H(pk_b)`
        3. Bob sends `addr_b` to Alice
        4. Alice posts Tx
            - input: 7 BTC
            - `utxo_B` for Bob: 5 BTC, `ScriptPK_b`
                - `ScriptPK_b`: `DUP HASH256 <addr_b> EQVERIFY CHECKSIG`
            - `utxo_A` for Alice: 2 BTC, `ScriptP_a`
            - locktime: 0
2. P2SH: pay to script hash
    - Payer specifies a redeem script (instead of just pkhash)
        1. Bob publishes `hash(redeem_script) <- bitcoin_addr`
        2. Alice sends funds to that address in funding Tx
        3. Bob can spend UTXO if he satisfy the script
    - `ScriptPK` in UTXO: `HASH160 H(redeem_script) EQUAL`
    - Payer can specify complex conditions for when UTXO can be spent
    - e.g. Multisig:
        - Goal: spending UTXO requires `t` out of `n` signatures (i.e. 2/3)
        - Redeem script for 2 of 3 (chosen by payer):
            - `<2> <PK1> <PK2> <PK3> <3> CHECKMULTISIG`
            - hash gives P2SH address
        - ScriptSig to spend (by Payee):
            - `<0> <sig1> <sig3> <redeem_script>`

### Segregated Witness

**ECDSA malleability**:

* Given `(m, sig)` anyone can create `(m, sig')` with `sig != sig'`
    - miner can change `sig` in Tx and change `TxID = SHA256(Tx)`
    - Tx issuer cannot tell what `TxID` is until Tx is posted
    - leads to problems and attacks
* With segregated witnesses, signature is moved to witness field in Tx
    - `TxID = Hash(Tx without witnesses)`