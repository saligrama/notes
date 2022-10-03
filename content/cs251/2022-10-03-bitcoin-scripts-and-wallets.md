# Bitcoin Scripts and Wallets

## Managing secret keys

* Users can have many PK/SK: per BTC/ETH/SOL/etc. addresses
* Wallets:
    - Generate PK/SK and store SK
    - Post and verify Tx
    - Show balances
* Types of wallets:
    - Cloud (e.g. Coinbase): like a bank, managed service
    - Laptop/phone: electrum, metamask
    - Hardware: Trezor, Ledger, Keystone, etc
    - Paper: print all sk on paper
    - Brain: memorize sk (bad idea)
    - Hybrid: non-custodial cloud wallet (using threshold signatures)
    - Need to safely manage keys: lose keys => lose funds

### Hardware wallets

* e.g. Ledger Nano X
* Connects to laptop or phone wallet using bluetooth or USB
    - Manages many secret keys
    - Each coin type is an app on top of OS
* PIN to unlock hardware: up to 48 digits
* Screen and buttons to verify and confirm Tx
* Backing up a hardware wallet:
    - Idea 1: generate a secret seed, where each PK is based on a HMAC of the seed
        - Seed is stored on HW device and in offline storage (as 24 words)
        - In case of loss, buy new device, restore seed, recompute keys
        - Open-source code to do this recomputation for you if hardware wallet manufacturer goes out of business